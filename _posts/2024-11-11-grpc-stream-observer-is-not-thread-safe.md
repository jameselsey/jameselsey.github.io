---
title: "The StreamObserver often used with gRPC streaming, is not thread safe!"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - java
  - grpc
---

I've been working with gRPC recently, in particular we had a bi-directional streaming API, whereby once a streaming connection is established, both the client and server can independently stream messages to each other.

After running the application for some time, with some test clients, we started to get some weird disconnect errors, it seemed to happen at random, and the stack traces were slightly different each time, here's a few examples of the errors I was seeing.

```
Interaction ends with status: Status{code=CANCELLED, description=Failed to read message., cause=io.grpc.StatusRuntimeException: INTERNAL: Invalid protobuf byte sequence
	at io.grpc.Status.asRuntimeException(Status.java:525)
	at io.grpc.protobuf.lite.ProtoLiteUtils$MessageMarshaller.parse(ProtoLiteUtils.java:240)
	at io.grpc.protobuf.lite.ProtoLiteUtils$MessageMarshaller.parse(ProtoLiteUtils.java:134)
	at io.grpc.MethodDescriptor.parseResponse(MethodDescriptor.java:284)
	at io.grpc.internal.ClientCallImpl$ClientStreamListenerImpl$1MessagesAvailable.runInternal(ClientCallImpl.java:657)
	at io.grpc.internal.ClientCallImpl$ClientStreamListenerImpl$1MessagesAvailable.runInContext(ClientCallImpl.java:644)
	at io.grpc.internal.ContextRunnable.run(ContextRunnable.java:37)
	at io.grpc.internal.SerializingExecutor.run(SerializingExecutor.java:133)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642)
	at java.base/java.lang.Thread.run(Thread.java:1583)
Caused by: com.google.protobuf.InvalidProtocolBufferException: Protocol message contained an invalid tag (zero).
	at com.google.protobuf.InvalidProtocolBufferException.invalidTag(InvalidProtocolBufferException.java:110)
	at com.google.protobuf.CodedInputStream$ArrayDecoder.readTag(CodedInputStream.java:607)	
```

```
Interaction ends with status: Status{code=CANCELLED, description=Failed to read message., cause=io.grpc.StatusRuntimeException: INTERNAL: Invalid protobuf byte sequence
	at io.grpc.Status.asRuntimeException(Status.java:525)
	at io.grpc.protobuf.lite.ProtoLiteUtils$MessageMarshaller.parse(ProtoLiteUtils.java:240)
	at io.grpc.protobuf.lite.ProtoLiteUtils$MessageMarshaller.parse(ProtoLiteUtils.java:134)
	at io.grpc.MethodDescriptor.parseResponse(MethodDescriptor.java:284)
	at io.grpc.internal.ClientCallImpl$ClientStreamListenerImpl$1MessagesAvailable.runInternal(ClientCallImpl.java:657)
	at io.grpc.internal.ClientCallImpl$ClientStreamListenerImpl$1MessagesAvailable.runInContext(ClientCallImpl.java:644)
	at io.grpc.internal.ContextRunnable.run(ContextRunnable.java:37)
	at io.grpc.internal.SerializingExecutor.run(SerializingExecutor.java:133)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642)
	at java.base/java.lang.Thread.run(Thread.java:1583)
Caused by: com.google.protobuf.InvalidProtocolBufferException: While parsing a protocol message, the input ended unexpectedly in the middle of a field.  This could mean either that the input has been truncated or that an embedded message misreported its own length.
	at com.google.protobuf.InvalidProtocolBufferException.truncatedMessage(InvalidProtocolBufferException.java:92)
	at com.google.protobuf.CodedInputStream$ArrayDecoder.pushLimit(CodedInputStream.java:1167)
	at com.google.protobuf.CodedInputStream$ArrayDecoder.readMessage(CodedInputStream.java:842)	
```

I wasted a lot of time trying to understand what was happening, checking the protobuf specs, adding logging everywhere, even trying to compare scenarios to find patterns. I tried everything that ChatGPT had, but couldn't resolve it.

Eventually I stumbled on [this thread](https://github.com/grpc/grpc-java/issues/2313) which points out that the StreamObserver is not thread safe, and I happened to be using background tasks on the server to initiate messages to the client, bingo!

My code was doing something like this:
```java
@KafkaListener
public void onMessage(ConsumerRecord<String, String> record) {
    // Do some processing
    
    // then send to grpc client
    streamObserver.onNext(response);
}
```
The `@KafkaListener` operates on a background thread, but you could have the same issue with other annotations like `@Scheduled` or `@Async`.
Which obviously has no concurrency controls, so I changed it to this:
```java
@KafkaListener
public void onMessage(ConsumerRecord<String, String> record) {
    // Do some processing
    
    // then send to grpc client via a synchronised client
    myGrpcClient.sendMessage(response);
}
```

I wrapped up the calls to the StreamObserver in a class like this

```java
public class MyGrpcClient {

    private final Object lock = new Object();
    private final StreamObserver<MyService.MyMessage> observer;

    public MyGrpcClient(StreamObserver<MyService.MyMessage> observer){
        this.observer = observer;
    }

    // Method to send messages in a thread-safe manner
    public void sendMessage(MyService.MyMessage message) {
        synchronized (lock) {
            observer.onNext(message);
        }
    }

    public void onCompleted() {
        synchronized (lock) {
            observer.onCompleted();
        }
    }
}

```

This means that in my Kafka listener, or anywhere else in the server where I may need to send messages to the client, I can go via the `MyGrpcClient` to do so, and be sure it's synchronised. This has worked well and completely resolved the errors I posted above.

Hopefully this helps you avoid wasting several days like I did ;)