---
title: 'A quick delve into Docker'
date: '2015-04-04T11:35:32+10:00'
permalink: /a-quick-delve-into-docker
author: 'James Elsey'
category:
    - DevOps
tag:
    - devops
    - docker
---
### The problem

When you run an application such as tomcat, you need to make sure you have the correct version of Java installed and configured, and then download the version of tomcat that is compatible with that version of Java. If you upgrade the version of Java, you’ve then got to setup a new JDK and potentially setup a new version of tomcat.

This is quite annoying as you’re going to have multiple versions of Java and tomcat installed on your machine, and at some point you’re going to get confused and have the wrong version running, or an environment variable not set correctly.

When you have an entire dev team doing the same thing, you’ll end up with people on different versions, different environment configurations, and ultimately you’ll get those “but it works on my environment” bugs at some point.

### The solution

Docker allows you to run applications in a container. It’s a bit like a VM, but without the OS. That doesn’t make much sense, and it didn’t to me either to start with. When you run a VM you’re running a full blown OS, and the hypervisor layer is bridging the kernel of the guest and the host.

If you’re just running apache on that VM it’s a bit of an overkill.

You can think of a container as being a stripped down linux OS. Theres barely anything there, just the bare bones; a filesystem and networking. Theres no gui, no pre-installed packages like apache, theres literally just enough to start the container. This makes them very lightweight and fast. It’s then up to you to install your own applications in those containers.

### Why is this good?

Don’t need a particular application anymore? Fine, delete the container. No need to hunt around your system manually uninstalling packages that you have scattered around.

Need to ship it to another machine? Publish the container and let others download it as an image ready to run.

When you start a new project, get your DevOps guy (or a dev) to build some containers for all of the dependencies of your project, you’ll probably need things like tomcat &amp; mysql which are easy because theres already official docker containers for those, but you may also need to build your own custom containers to stub your integration points, or to install integration point software in a stub mode. Then, when your project kicks off and the devs are ready to get started, all they need to do is pull the images and run them, and they’ve got a full stack dev environment ready to use. Marvellous.

As I’m new to Docker, perhaps I’m not the best at explaining it. I’d highly recommend you watch this:

<iframe allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="435" src="https://www.youtube.com/embed/FdkNAjjO5yQ?feature=oembed" title="Docker for Developers - Jérôme Petazzoni" width="580"></iframe>

Lets have a look at a few containers, tomcat and mysql.

### Tomcat

You could build your own tomcat container, but theres an [official image](https://registry.hub.docker.com/_/tomcat/) that you can use, start it up using:

```
docker run -it --rm -p 8888:8080 tomcat:8.0
```

The docker run command is going to start a container from an image, as you likely won’t have that image, it will realise this and then pull it. You could pull it separately using a docker pull command, but the run figures this out for us.

The **it** flag is for interactive mode, so you can see the output, in this case, the output of catalina being executed. An alternative would be to use the d flag which runs it as a daemon (background task).

The **rm** flag is for automatically removing the container if it exits, you don’t strictly need this, but the tomcat official image page suggests you include it.

The **-p** flag tells docker to forward port 8080 on the container to 8888 on the host, so we can access tomcat outside of the container, it’d be a bit pointless without this. An alternative flag would be **-P** which forwards all ports.

The tomcat:8.0 is the image name along with its tag.

Run the docker run command and you should see the output of the catalina start process. You can open another tab and run docker ps to see its process state.

Now lets try and access the tomcat manager page, in order to do so you need to get the IP of the boot2docker instance. Remember, that boot2docker is the docker host, not the laptop, so you need to access containers via boot2dockers vm. It took me a little while to[ realise this](http://stackoverflow.com/a/27476982/155695), I was running a **docker inspect** on the container, finding the network settings/IP and trying to access that, not realising that its actually the boot2docker vm you need to access.

You can easily do this by obtaining the ip using **boot2docker ip**. Then you can access:  
http://boot2docker-ip:8888/

Thats it, you should be on the tomcat page now. I’ll leave it up to you to make use of it, perhaps extend the tomcat image and deploy your own applications?

### MySql

As with tomcat, there is an [officially supported MySql container](https://registry.hub.docker.com/_/mysql/), download it using “docker pull” like so.

```
docker pull mysql:5.7
Pulling repository mysql
463d9ebad128: Download complete
```

Run up that mysql image using

```
docker run -d -p 3306:3306 mysql
2eac3ea6b64e8156f2d81fac3e2336153c7a66b54c524574958dee7888787284
```

The **-d** runs the container as a daemon (background task) and returns you the container id.

Next have a look at **docker ps** to confirm its running

```
docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
2eac3ea6b64e mysql:latest "/usr/bin/mysqld_saf 5 seconds ago Up 2 seconds 0.0.0.0:3306-&gt;3306/tcp pensive_heisenberg
```

Now lets try to connect to it

```
docker run -it --link myfirstmysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

This is spawning another container which will run the mysql client. At first, I was trying to use the mysql client on my local machine to connect to it, but then I realised that I was missing the point of docker, why install mysql locally, just to use the client, when really I could be doing that via docker?

see [https://registry.hub.docker.com/\_/mysql/](https://registry.hub.docker.com/_/mysql/)

### Round up

I’m yet to use this in anger, but I can already think of applications on previous projects that would have benefitted from this. I’ve been using virtualisation with vagrant and chef for a while, so I’m interested to see how different things will work out by using docker.