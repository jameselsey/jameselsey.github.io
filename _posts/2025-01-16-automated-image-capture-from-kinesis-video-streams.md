---
title: "How to setup Automated Image Generation in Kinesis Video Streams to create images in S3"
excerpt_separator: "<!--more-->"
permalink: /kvs-auto-image-generation/
categories:
  - Blog
tags:
  - aws
  - gstreamer
  - kinesis-video-streams
  - s3

---

## S3 Image generation not working?

In 2024, AWS [announced a new feature](https://aws.amazon.com/about-aws/whats-new/2022/05/amazon-kinesis-video-streams-announces-managed-support-image-extraction/) for Kinesis Video Streams, which is a managed service where they can automatically take images from a video stream and store them into S3, without you having to write any code, perfect!

Having followed the [documentation](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/images.html), I wasn't able to get this working, after wasting hours, I was able to get it working, so I thought I'd save others some time by documenting what I did.


## Data into KVS

It doesn't really matter how you're streaming video into KVS, but for the record I'm using the `kvssink` gstreamer plugin, from the Raspberry Pi. My pipeline looked like this, with the relevant part being the last line

```
gst-launch-1.0 shmsrc socket-path=/tmp/infered.feed do-timestamp=1 ! \
        video/x-raw,format=RGB,width=1920,height=1080,framerate=30/1 ! \
        queue max-size-buffers=2 leaky=downstream ! \
        videorate ! video/x-raw,framerate=30/1 ! \
        videoconvert ! video/x-raw,format=I420 ! \
        x264enc key-int-max=25 tune=zerolatency speed-preset=ultrafast bitrate=1000 ! \
        h264parse ! \
        kvssink stream-name="inferred" access-key="$AWS_ACCESS_KEY" secret-key="$AWS_SECRET_ACCESS_KEY" aws-region="$AWS_REGION" max-latency=100
```

## KVS to S3
Following the documentation, you'll create a file like this

```
{
 "StreamName": "TestStream",
 "ImageGenerationConfiguration":
 {
  "Status": "ENABLED",
  "DestinationConfig":
  {
   "DestinationRegion": "us-east-1",
   "Uri": "s3://bucket-name"
  },
  "SamplingInterval": 200,
  "ImageSelectorType": "PRODUCER_TIMESTAMP",
  "Format": "JPEG",
  "FormatConfig": {
                "JPEGQuality": "80"
       },
  "WidthPixels": 320,
  "HeightPixels": 240
 }
}
```

and then apply it using this

```
aws kinesisvideo update-image-generation-configuration \
--cli-input-json file://./update-image-generation-input.json \
```

Well, that still didn't work for me, so I had to do some more digging.

## Permissions?

As mentioned in [this youtube video about image extraction](https://www.youtube.com/watch?v=pUvxI76YnfA&t=148s), the user that kvssink is using (as in, the user the key & secret key belongs to) is the user that's used when KVS tries to write to s3, so you need to make sure that has permissions on S3.

For demo purposes, I just gave it full access to S3, and I also updated the bucket policy to allow the kinesis service access.

Nope, still not working.

## The fix
After some digging, I found that the KVS producer (in my case, kvssink), needs to send tags to the stream to tell it to use the image generation.

I tried to use the `--stream-tags`, as [suggested elsewhere](https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp/discussions/960), but that didn't work for me.

Then I stumbled on [this PR](https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp/pull/1174) to the KVS producer. This was it, this is what worked for me.
I had to check out the fork and rebuild the SDK (same steps as in the README), and then I was able to add the tags to the stream by using the `img-gen=1` on the kvssink

That PR was closed in favour of [another one](https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp/pull/1176), but that still hasn't been merged yet, so I'm not sure if this will be the final solution, but it worked for me.

Am I doing something wrong here? Why was this so difficult to setup, and the SDK still not updated to include this feature? Well, hopefully you don't waste a bunch of hours like I did.
