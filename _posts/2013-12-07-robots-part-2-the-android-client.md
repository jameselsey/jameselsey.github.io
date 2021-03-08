---
title: 'Robots! Part 2, the android client'
date: '2013-12-07T12:01:04+10:00'
permalink: /robots-part-2-the-android-client
author: 'James Elsey'
category:
    - Android
tag:
    - android
    - async-task
    - lego
    - pi
    - raspberry-pi
    - robotics
    - socket
---
Continuing on from my [previous post](/robots-part-1), I’ve created an android client that I can use to send commands to my python server.

Ultimately I want to be able to control the robot remotely, the best way to do this would be to control the robot from a tablet or a phone which communicates wirelessly with the pi via bluetooth or wifi. In my [previous post](/robots-part-1) I described setting up a python application that will run on the raspberry pi and listen for commands. All I needed to do was to create a very basic android interface that can send commands to the raspberry pi.

The robot I intend to build will be based on tracks instead of wheels, there are many benefits to this but the most significant is that from an engineering perspective is that its much easier to build. A car needs forward and backwards drive, but also sideways drive for the front axle. In my opinion, it is far simpler to have a tracked vehicle with a motor controlling each side. When both motors are turned in the same direction the vehicle moves forward or backwards, and when the motors run in opposite directions the vehicle will turn on the spot.

My app interface mimics the layout of the vehicle itself, with an up and down button on the left and right hand side of the screen, as shown below.

![Arrow buttons for controlling robots tracks](/assets/post_images/2013/layout-2013-12-04-072527.png)

You can see the code for this layout on the github repo [here](https://github.com/jameselsey/robots/blob/master/client/Robots/Robots/src/main/res/layout/activity_main.xml).

I want the user to be able to hold a button and the motor will run until they take their finger off. For this I’ve attached [listeners](http://developer.android.com/reference/android/view/View.OnTouchListener.html) on the buttons that will listen for the key up and key down events. It will send separate events for starting and stopping the motors, like so:

```java
        Button leftForward = (Button) findViewById(R.id.leftForward);

        leftForward.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View view, MotionEvent motionEvent) {
                switch (motionEvent.getAction()) {
                    case MotionEvent.ACTION_DOWN: {
                        sendCommand(leftForwardCommand + "-Start");
                        break;
                    }
                    case MotionEvent.ACTION_UP: {
                        sendCommand(leftForwardCommand + "-Stop");
                        break;
                    }
                }
                return false;
            }
        });
```

As the sendCommand needs send a message over the network, I need to take this off the main UI thread otherwise I’d get an exception such as:

```
android.os.NetworkOnMainThreadException
```

To take this off the UI thread, I simply move the sending of the command into an [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html), like so:

```java
private void sendCommand(String command) {
        new SendCommandTask().execute(command);
    }

    class SendCommandTask extends AsyncTask<String, Void, Void> {

        @Override
        protected Void doInBackground(String... commands) {
            String command = commands[0];
            try {
                //TODO: make this configurable inside the app
                Socket socket = new Socket("192.168.0.6", 3033);
                PrintWriter out = new PrintWriter(new BufferedWriter(new OutputStreamWriter(socket.getOutputStream())), true);
                out.println(command);

                Log.d(TAG, "Successfully sent " + command);
            } catch (IOException ioe) {
                Log.d(TAG, "Unable to send command", ioe);
            }
            return null;
        }
    }

```

Now if I run the python server and then send press the buttons in the app, I see this output from the python application.

```
Server listening on port 3033...
...L-FORWARD-Start…..L-FORWARD-Stop….
```

Thats all for now, you can access this code on my [github repository](https://github.com/jameselsey/robots). Next post will either be in relation to building the robot, or using the BrickPi APIs.