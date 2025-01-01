---
title: "How to stream video from a Raspberry Pi to AWS Kinesis Video Streams using GStreamer"
excerpt_separator: "<!--more-->"
permalink: /gstreamer-pi-to-kvs/
categories:
  - Blog
tags:
  - aws
  - gstreamer
  - kinesis-video-streams
  - raspberry-pi
---

## kvssink would not stream video to Kinesis Video Streams

![Raspberry Pi 5 with Pi Camera 3 and the 26 TOPS AI HAT](/assets/post_images/2025/gstreamer-pi-to-kvs/pi5.jpg)


Impressed by what I saw at AWS re:invent 2024, I decided to purchase a Raspberry Pi 4, a Pi Camera 3, and the new 26 TOPS AI HAT to experiment. One of the things I wanted to do over the Christmas break is understand how I can stream video so it can be processed by YOLO (more on that in another post to come), but also how do I stream into AWS Kinesis Video Streams.

For the Pi to stream video to KVS, you need the kvssink plugin for the gstreamer framework. In order to get that, you need to install the Producer SDK, I followed all the instructions, confirmed that kvssink was available, went to run my pipeline... and nothing happened.

My pipeline was running without error, yet nothing was making into KVS, here's what I had

```
# this wasn't working, but showing no errors
GST_DEBUG=3 gst-launch-1.0 libcamerasrc ! \
    videoconvert ! \
    video/x-raw,width=640,height=480,framerate=30/1,format=I420 ! \
    x264enc ! \
    h264parse ! \
    video/x-h264,stream-format=avc,alignment=au,profile=baseline ! \
    kvssink stream-name="demo_stream" access-key="snip" secret-key="snip" aws-region="ap-southeast-2"
```

```
GST_DEBUG=3 gst-launch-1.0 libcamerasrc ! \
videoconvert ! \
video/x-raw,format=I420,width=640,height=480,framerate=30/1 ! \
x264enc key-int-max=25 tune=zerolatency ! \
h264parse ! \
video/x-h264,stream-format=avc,alignment=au,width=640,height=480,framerate=30/1,profile=baseline ! \
kvssink stream-name="demo_stream" access-key="snip" secret-key="snip" aws-region="ap-southeast-2"
```


I could see in the output that it was successfully negotiating with KVS, but why wasn't anything coming through?

```
[DEBUG] [01-01-2025 04:12:47:153.807 GMT] describeStreamCurlHandler(): [demo_stream] DescribeStream API response: {"StreamInfo":{"CreationTime":1.735442939457E9,"DataRetentionInHours":24,"DeviceName":null,"IngestionConfiguration":null,"KmsKeyId":"arn:aws:kms:ap-southeast-2:429276963313:alias/aws/kinesisvideo","MediaType":null,"Status":"ACTIVE","StreamARN":"arn:aws:kinesisvideo:ap-southeast-2:429276963313:stream/demo_stream/1735442939457","StreamName":"demo_stream","Version":"FhH7KJXkTQSLfXV123cU"}}
[INFO ] [01-01-2025 04:12:47:154.961 GMT] describeStreamResultEvent(): Describe stream result event.
[WARN ] [01-01-2025 04:12:47:155.009 GMT] describeStreamResult(): Retention period returned from the DescribeStream call doesn't match the one specified in the StreamInfo
[WARN ] [01-01-2025 04:12:47:155.021 GMT] describeStreamResult(): Content type returned from the DescribeStream(null) call doesn't match the one specified in the StreamInfo(video/h264)
[WARN ] [01-01-2025 04:12:47:155.031 GMT] describeStreamResult(): [demo_stream] Retention period returned from the DescribeStream call doesn't match the one specified in the StreamInfo
[WARN ] [01-01-2025 04:12:47:155.040 GMT] describeStreamResult(): [demo_stream] Content type returned from the DescribeStream call doesn't match the one specified in the StreamInfo
[DEBUG] [01-01-2025 04:12:47:155.389 GMT] setRequestHeader(): Appending header to request: user-agent -> AWS-SDK-KVS-CPP-CLIENT/3.4.2/1.5.3 GCC/12.2.0 Linux/6.6.62+rpt-rpi-2712 aarch64 CPPSDK
[DEBUG] [01-01-2025 04:12:47:155.477 GMT] setRequestHeader(): Appending header to request: host -> kinesisvideo.ap-southeast-2.amazonaws.com/getDataEndpoint
[DEBUG] [01-01-2025 04:12:47:155.505 GMT] setRequestHeader(): Appending header to request: X-Amz-Date -> 20250101T041247Z
[DEBUG] [01-01-2025 04:12:47:155.517 GMT] setRequestHeader(): Appending header to request: content-type -> application/json
[DEBUG] [01-01-2025 04:12:47:155.542 GMT] setRequestHeader(): Appending header to request: content-length -> 57
[DEBUG] [01-01-2025 04:12:47:155.581 GMT] setRequestHeader(): Appending header to request: Authorization -> AWS4-HMAC-SHA256 Credential=snip/20250101/ap-southeast-2/kinesisvideo/aws4_request, SignedHeaders=host;user-agent;x-amz-date, Signature=snip
[INFO ] [01-01-2025 04:12:47:412.504 GMT] writeHeaderCallback(): RequestId: 741214aa-b61b-4d86-b4c1-3a94ff6cac12
[DEBUG] [01-01-2025 04:12:47:412.587 GMT] getStreamingEndpointCurlHandler(): [demo_stream] GetStreamingEndpoint API response: {"DataEndpoint":"https://s-ca658586.kinesisvideo.ap-southeast-2.amazonaws.com"}
[INFO ] [01-01-2025 04:12:47:413.802 GMT] getStreamingEndpointResultEvent(): Get streaming endpoint result event.
[DEBUG] [01-01-2025 04:12:47:413.854 GMT] getStreamingTokenHandler invoked
[DEBUG] [01-01-2025 04:12:47:413.868 GMT] Refreshing credentials. Force refreshing: 1 Now time is: 1735704767413865404 Expiration: 18446744073709551615
[INFO ] [01-01-2025 04:12:47:413.892 GMT] getStreamingTokenResultEvent(): Get streaming token result event.
[DEBUG] [01-01-2025 04:12:47:413.910 GMT] streamReadyHandler invoked
```

Well, I believe it was because the caps weren't being set correctly on the h264 parsing, I needed to set `width=640,height=480,framerate=30/1`

I then ran this, and it worked! I had a video of my handsome self showing up in the KVS console, albeit with a 10 second delay.

```
GST_DEBUG=3 gst-launch-1.0 libcamerasrc ! \
videoconvert ! \
video/x-raw,format=I420,width=640,height=480,framerate=30/1 ! \
x264enc key-int-max=25 tune=zerolatency ! \
h264parse ! \
video/x-h264,stream-format=avc,alignment=au,width=640,height=480,framerate=30/1,profile=baseline ! \
kvssink stream-name="demo_stream" access-key="snip" secret-key="snip" aws-region="ap-southeast-2"
```

![Kinesis Video Streams sample Media Playback](/assets/post_images/2025/gstreamer-pi-to-kvs/kvs-media-playback.png)


## Reducing the video delay
For the project I have in mind, a 10 second delay would be perfectly fine, even longer perhaps, but I wanted to see if I could reduce the delay without sacrificing too much quality. A smaller delay would help me iterate faster.

Firstly, I did not need the `videoconvert`, this is because the Pi camera 3 is able to output in I420 format, so there was no need for the explicit conversion.

Next I added `key-int-max=25 tune=zerolatency speed-preset=ultrafast bitrate=1000` to the x264enc stage, this should reduce the delay, but it may also reduce the quality. You can reduce the bitrate down to 500, but the video quality really suffers then.

The `max-latency=100` on the kvssink stage is also important, this is the maximum latency that the sink will allow, if the latency goes over this, the sink will drop frames to catch up. This essentially helps kvssink keep a smaller buffer.
```
GST_DEBUG=3 gst-launch-1.0 libcamerasrc ! \
video/x-raw,format=I420,width=640,height=480,framerate=30/1 ! \
x264enc key-int-max=25 tune=zerolatency speed-preset=ultrafast bitrate=1000 ! \
h264parse ! \
video/x-h264,stream-format=avc,alignment=au,width=640,height=480,framerate=30/1,profile=baseline ! \
kvssink stream-name="demo_stream" access-key="snip" secret-key="snip" aws-region="ap-southeast-2" max-latency=100
```

## Some other hurdles that caught me out
Downloading and compiling the C++ SDK for KVS producer wasn't a great experience, it's been so long since I've had to do this, and have been spoiled by `brew install` or `sudo apt install`, or even `docker pull`. I don't know why, but the instructions didn't work perfectly for me, I had to symlink the `kvs_log_configuration` file because the sdk didn't install inside the `build` directory, even though I copied the instructions exactly.

Next, none of the example scripts included with the SDK would work on the Pi, because they were constructed to use v4l2src, which is not available on the Pi. This made it difficult to confirm the SDK was working. There was a lot of trial and error with writing my own gstreamer pipelines with libcamerasrc instead.

Lastly, surely I can't be the only one trying to play with this stuff, but I found very little information online, no blog posts, and perhaps 1 or 2 youtube videos that were several years old. Well, hopefully this post will help someone out.
