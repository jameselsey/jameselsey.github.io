---
title: "How to do object detection and tracking on a Raspberry Pi with the AI HAT with Hailo"
excerpt_separator: "<!--more-->"
permalink: /object-tracking-with-pi5-and-ai-hat/
categories:
  - Blog
tags:
  - aws
  - gstreamer
  - computer-vision
  - raspberry-pi

---

## Struggling to get it working?

Let me guess, you've recently bought the AI HAT from Raspberry Pi and you can't figure out how to get object detection with tracking working? You've read the blog from [Core Electronics](https://core-electronics.com.au/guides/yolo-object-detection-on-the-raspberry-pi-ai-hat-writing-custom-python/#running-object-detection-demo), and you've read [this post](https://www.hackster.io/naveenbskumar/car-detection-and-tracking-system-with-rpi-hailo-ai-kit-16ef44) on Car Tracking on Hackster, but still can't get it working? Hopefully this post will solve it for you, I've sunk many, many hours of trial and error into this! 

![Pi 5, Pi Camera, AI Hailo HAT](/assets/post_images/2025/object-tracking/pi-ai-hailo.jpg)

## Setup

I'm going to assume you've already got the Pi setup, and have installed the Hailo SDK, if not, follow the instructions on the [Hailo website](https://www.hailo.ai/developers/). Don't forget that you'll need the [Hailo Tappas SDK](https://github.com/hailo-ai/tappas) setup too, as that provides gstreamer plugins that we'll be using. There's also a Hailo guide on setting up the Pi 5 [here](https://github.com/hailo-ai/hailo-rpi5-examples/blob/main/doc/install-raspberry-pi5.md).

## gstreamer pipeline

Abstracted from the code, this is the pipeline that worked for me

```
shmsrc socket-path=/tmp/feed.raw do-timestamp=true is-live=true ! \
    video/x-raw, format=NV12, width=1920, height=1080, framerate=30/1 ! \
    videoconvert ! \
    video/x-raw, format=RGB, width=1920, height=1080, framerate=30/1 ! \
    hailocropper \
        so-path=/home/jelsey/dev/cvml-at-the-edge/components/consumer-tracking/libwhole_buffer.so \
        function-name=create_crops \
        use-letterbox=true \
        resize-method=inter-area \
        internal-offset=true \
        name=cropper1 \
    hailoaggregator name=agg1 \
    cropper1. ! \
        queue name=bypass1_q leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        agg1. \
    cropper1. ! \
        queue leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        videoscale qos=false n-threads=2 ! \
        video/x-raw, pixel-aspect-ratio=1/1 ! \
        queue leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        hailonet \
            hef-path=yolov8m.hef \
            batch-size=1 ! \
        queue leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        hailofilter \
            so-path=/home/jelsey/dev/cvml-at-the-edge/components/consumer-tracking/libyolo_hailortpp_postprocess.so \
            qos=false ! \
        queue leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        agg1. \
    agg1. ! \
        hailotracker \
            name=hailo_tracker \
            keep-tracked-frames=3 \
            keep-new-frames=3 \
            keep-lost-frames=3 ! \
        queue name=queue_user_callback leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        identity name=identity_callback signal-handoffs=true ! \
        queue leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        hailooverlay qos=false ! \
        queue leaky=no max-size-buffers=30 max-size-bytes=0 max-size-time=0 ! \
        videoconvert n-threads=2 qos=false ! \
        shmsink socket-path=/tmp/infered.feed sync=false wait-for-connection=false
```

Some notes on this pipeline:
1. I'm using a shared memory source, this is because I'm using a separate process to capture the video, and then I'm using this pipeline to process it. This is because the Pi only permits one process to read the camera at a time, so having a pipeline to stream to shared memory means that I can setup multiple consumers of that. You can replace this with `libcamerasrc` if you're using a camera.
2. I'm using the [hailocropper](https://github.com/hailo-ai/tappas/blob/master/docs/elements/hailo_cropper.rst) and [hailoaggregator](https://github.com/hailo-ai/tappas/blob/master/docs/elements/hailo_aggregator.rst) to split the stream and rejoin it. This allows me to infer on a smaller frame size, but then overlay the bounding boxes on the original full size frame.
3. I'm using [yolov8m](https://github.com/hailo-ai/hailo_model_zoo/blob/master/docs/public_models/HAILO8/HAILO8_object_detection.rst).hef, which will automatically set the frame to 640x640 whether you downscale it yourself or not. See the [models page](https://github.com/hailo-ai/hailo_model_zoo/blob/master/docs/public_models/HAILO8/HAILO8_object_detection.rst) for more info.
4. After the split and aggregation, then I'm pushing the frames through the [hailotracker](https://github.com/hailo-ai/tappas/blob/master/docs/elements/hailo_tracker.rst), which uses a rolling buffer of 3 frames to determine if the same object is present, which is then assigned a tracking ID.
5. After tracking, there's an `identity`, this gives me a hook into some python code, so that I can look at the tracked items. I'm not really interested in the data prior to tracking, I want to wait until it's tracked before I do anything with it. See below for the python code.
6. The [hailooverlay](https://github.com/hailo-ai/tappas/blob/master/docs/elements/hailo_overlay.rst) is used to draw the bounding boxes on the frame, which includes the object class, it's confidence, and tracking ID.
7. Lastly, I'm streaming the frames back to [shared memory](https://gstreamer.freedesktop.org/documentation/shm/shmsink.html?gi-language=python#shmsink-page), so that I can decouple this from what happens next.


## Python code for the user callback

```python
import gi
gi.require_version('Gst', '1.0')
from gi.repository import Gst, GLib
import os
import numpy as np
import cv2
import hailo
import pprint
import supervision as sv
from hailo_rpi_common import (
    get_caps_from_pad,
    get_numpy_from_buffer,
    app_callback_class,
)
from detection_pipeline import GStreamerDetectionApp
import boto3
import json

tracker = sv.ByteTrack()
label_annotator = sv.LabelAnnotator()

# Set to keep track of emitted tracking IDs
emitted_ids = set()

# Define the target classes for detection
# this should align to the COCO list of objects
target_classes = {'person', 'car', 'bus', 'truck', 'motorcycle'}


# Setup the iot client for sending MQTT
accessKey = os.getenv("AWS_ACCESS_KEY")
secretKey = os.getenv("AWS_SECRET_ACCESS_KEY")
region = os.getenv("AWS_REGION")
topic = "detections"
client = boto3.client(
        "iot-data",
        region_name=region,
        aws_access_key_id=accessKey,
        aws_secret_access_key=secretKey
    )

# -----------------------------------------------------------------------------------------------
# User-defined class to be used in the callback function
# -----------------------------------------------------------------------------------------------
# Inheritance from the app_callback_class
class user_app_callback_class(app_callback_class):
    def __init__(self):
        super().__init__()
        self.new_variable = 42  # New variable example

    def new_function(self):  # New function example
        return "The meaning of life is: "

# -----------------------------------------------------------------------------------------------
# User-defined callback function
# -----------------------------------------------------------------------------------------------

# This is the callback function that will be called when data is available from the pipeline
def app_callback(pad, info, user_data):
    # Get the GstBuffer from the probe info
    buffer = info.get_buffer()
    if buffer is None:
        return Gst.PadProbeReturn.OK

    # Increment frame count
    user_data.increment()

    # Get the caps from the pad
    format, width, height = get_caps_from_pad(pad)

    # Retrieve the video frame if required
    frame = None
    if user_data.use_frame and format and width and height:
        frame = get_numpy_from_buffer(buffer, format, width, height)

    # Extract detections from the buffer
    roi = hailo.get_roi_from_buffer(buffer)
    hailo_detections = roi.get_objects_typed(hailo.HAILO_DETECTION)

    # Filter detections to include only target classes
    filtered_detections = [
        detection for detection in hailo_detections
        if detection.get_label() in target_classes
    ]

    # Prepare detection data for Supervision
    boxes = []
    confidences = []
    class_ids = []

    for detection in filtered_detections:
        tracking_id = detection.get_objects_typed(hailo.HAILO_UNIQUE_ID)[0].get_id()
        # Emit event only if the tracking ID hasn't been emitted before
        if tracking_id not in emitted_ids:
            print(f"Detection!: {tracking_id} {detection.get_label()} {detection.get_confidence():.2f}\n")
            emitted_ids.add(tracking_id)
            payload = {
                "tracking_id" : tracking_id,
                "label": detection.get_label()
            }
            response = client.publish(topic=topic, qos=1, payload=json.dumps(payload))
            print(f"Message published: {response}")

        label = detection.get_label()
        bbox = detection.get_bbox()
        confidence = detection.get_confidence()
        boxes.append([bbox.xmin() * width, bbox.ymin() * height, bbox.xmax() * width, bbox.ymax() * height])
        confidences.append(confidence)
        class_ids.append(label)  # Ensure label is an integer class ID

    return Gst.PadProbeReturn.OK


if __name__ == "__main__":
    # Create an instance of the user app callback class
    user_data = user_app_callback_class()
    app = GStreamerDetectionApp(app_callback, user_data)
    app.run()
    
```

Let's cover a few important things in the code.

Using the yolov8m pretrained model, it'll detect whatever objects it can from the [COCO list of objects](https://github.com/amikelive/coco-labels/blob/master/coco-labels-2014_2017.txt), in my case I'm only interested in certain objects so I specify these at the top. It's important to note, that hailo will detect everything, but the callback itself can do filtering to reduce what goes to MQTT, in my case, viewing the output I still see bounding boxes for "stopsign", but that's being filtered prior to MQTT.
```python
# Define the target classes for detection
# this should align to the COCO list of objects
target_classes = {'person', 'car', 'bus', 'truck', 'motorcycle'}
```

This is the part that gets the unique tracking ID for an object, this is the integer that you see on the bottom left of the bounding box.
```python
tracking_id = detection.get_objects_typed(hailo.HAILO_UNIQUE_ID)[0].get_id()
```

This is the part that I use to send an MQTT message, notice how I keep track of previously emitted tracking IDs, this is because each frame is passing through this logic, and we only want to emit the object once. There are some drawbacks to this of course, as sometimes the object is detected with a low confidence, but we see it in another frame as a different object class. I've noticed this more with objects that are far away, as they get closer they may get re-classified. There's lots of things we can do such as detecting from certain areas of view that are closer, using better trained models, or making decisions based on confidence, but for my demo, this is fine.

## Conclusion

<iframe width="480" height="360" src="https://youtu.be/BssT-Pm8uEc" > </iframe>

This is still reasonably early days, as the AI HAT was only released last year, but I've certainly found that the hailo documentation is spread thin, you have to check several repos, and there's very little from the community out there, so I had to do a lot of trial and error to get this working.

Having said that, the results are fantastic, I was absolutely blown away when I got this working. I was able to track multiple objects at 30fps in 1920x1080p and the output was smooth. This was very impressive for a pre trained model, I imagine the results would be even better with a customised model and some tweaking of the pipeline parameters.

Have a look at my [cvml-at-the-edge github repo](https://github.com/jameselsey/cvml-at-the-edge/tree/main/components/consumer-tracking), this is in the `consumer-tracking` directory
