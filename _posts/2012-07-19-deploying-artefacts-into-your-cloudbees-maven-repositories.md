---
title: 'Deploying artefacts into your CloudBees maven repositories.'
date: '2012-07-19T10:50:59+10:00'
permalink: /deploying-artefacts-into-your-cloudbees-maven-repositories
author: 'James Elsey'
category:
    - 'Cloud'
    - 'Continuous Integration'
    - Maven
tag:
    - cloud
    - cloudbees
    - maven
    - repository

---
If you’re like me, and have a fair few hobby projects on the go at any one time, you’re may want to take advantage of some of the free cloud services that are available out there. I’m particulary fond of the Maven repositories that [CloudBees](http://www.CloudBees.com) offer, as it gives you a safe and easy way to deploy your artefacts, whether they’re 3rd party libraries, snapshots, or even releases of your own software.

Head over to CloudBees.com and register an account, its free.

I’ve recently been doing a fair bit of work 3rd party libraries that are not available on the maven repositories, you have to download the source and compile it yourself (I have [another post about an example](/tesseract-ocr-on-android-is-easier-if-you-maven-ise-it-works-on-windows-too) of that). Since I often develop on different machines, and I’m not actually changing the library, I wanted a single place where I could upload a pre compiled copy of it and just depend on it as I would any other library, such as JUnit.

The first task, is to get your project compiling and working locally, once that is done you can then add the following into your pom.xml

Repository (that references a configured repository on ~/.m2/settings.xml)

```xml
<repository>
            <id>cloudbees-private-snapshot-repository</id>
            <name>cloudbees-private-snapshot-repository</name>
            <url>dav:https://repository-jameselsey.forge.cloudbees.com/snapshot/</url>
        </repository>
```

Also, since maven 3 requires the webdav extension, you’ll need to add this into the build/extensions section of your pom.xml:

```xml
<extensions>
            <!-- Extension required to deploy a snapshot or a release to the CloudBees remote maven repository using Webdav -->
            <extension>
                <groupId>org.apache.maven.wagon</groupId>
                <artifactId>wagon-webdav</artifactId>
                <version>1.0-beta-2</version>
            </extension>
        </extensions>
```

After that, make sure that your ~/.m2/settings.xml file contains details of your CloudBees repositories, here is my copy, make sure you put your password in, and replace occurences of “jameselsey” with your own user ID.

```xml
<?xml version="1.0"?>
<!--
Licensed to the Apache Software Foundation (ASF) under one or more contributor license agreements. See the NOTICE file distributed with this work for additional information regarding copyright ownership. The ASF licenses this file to you under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0 Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
<servers>
    <server>
        <id>cloudbees-private-snapshot-repository</id>
        <username>jameselsey</username>
        <password>YOUR_PASSWORD_HERE</password>
        <filePermissions>664</filePermissions>
        <directoryPermissions>775</directoryPermissions>
    </server>
    <server>
        <id>cloudbees-private-release-repository</id>
        <username>jameselsey</username>
        <password>YOUR_PASSWORD_HERE</password>
        <filePermissions>664</filePermissions>
        <directoryPermissions>775</directoryPermissions>
    </server>
    <server>
        <id>cloudbees-private-snapshot-plugin-repository</id>
        <username>jameselsey</username>
        <password>YOUR_PASSWORD_HERE</password>
        <filePermissions>664</filePermissions>
        <directoryPermissions>775</directoryPermissions>
    </server>
    <server>
        <id>cloudbees-private-release-plugin-repository</id>
        <username>jameselsey</username>
        <password>YOUR_PASSWORD_HERE</password>
        <filePermissions>664</filePermissions>
        <directoryPermissions>775</directoryPermissions>
    </server>
</servers>
<profiles>
    <profile>
        <id>cloudbees.private.release.repository</id>
        <activation>
            <property>
                <name>!cloudbees.private.release.repository.off</name>
            </property>
        </activation>
        <repositories>
            <repository>
                <id>cloudbees-private-release-repository</id>
                <url>https://repository-jameselsey.forge.cloudbees.com/release</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </repository>
        </repositories>
    </profile>
    <profile>
        <id>cloudbees.private.snapshot.repository</id>
        <activation>
            <property>
                <name>!cloudbees.private.snapshot.repository.off</name>
            </property>
        </activation>
        <repositories>
            <repository>
                <id>cloudbees-private-snapshot-repository</id>
                <url>https://repository-jameselsey.forge.cloudbees.com/snapshot</url>
                <releases>
                    <enabled>false</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
        </repositories>
    </profile>
    <profile>
        <id>cloudbees.private.release.plugin.repository</id>
        <activation>
            <property>
                <name>!cloudbees.private.release.plugin.repository.off</name>
            </property>
        </activation>
        <pluginRepositories>
            <pluginRepository>
                <id>cloudbees-private-release-plugin-repository</id>
                <url>https://repository-jameselsey.forge.cloudbees.com/release</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile>
    <profile>
        <id>cloudbees.private.snapshot.plugin.repository</id>
        <activation>
            <property>
                <name>!cloudbees.private.snapshot.plugin.repository.off</name>
            </property>
        </activation>
        <pluginRepositories>
            <pluginRepository>
                <id>cloudbees-private-snapshot-plugin-repository</id>
                <url>https://repository-jameselsey.forge.cloudbees.com/snapshot</url>
                <releases>
                    <enabled>false</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile>
</profiles>
</settings>
```

Now, if you run **mvn clean install deploy:deploy**, maven will create a clean build of your application, install it to your local repository, and then deploy it to your CloudBees snapshot repo.

```
[INFO] --- maven-deploy-plugin:2.7:deploy (default-cli) @ tess-two ---
WAGON_VERSION: 1.0-beta-2
Downloading: dav:https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/maven-metadata.xml
Downloaded: dav:https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/maven-metadata.xml (769 B at 0.7 KB/sec)
Uploading: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/tess-two-0.0.1-20120720.120205-2.apklib
Uploaded: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/tess-two-0.0.1-20120720.120205-2.apklib (5865 KB at 449.0 KB/sec)
Uploading: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/tess-two-0.0.1-20120720.120205-2.pom
Uploaded: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/tess-two-0.0.1-20120720.120205-2.pom (3 KB at 1.2 KB/sec)
Downloading: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/maven-metadata.xml
Downloaded: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/maven-metadata.xml (276 B at 0.6 KB/sec)
Uploading: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/maven-metadata.xml
Uploaded: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/0.0.1-SNAPSHOT/maven-metadata.xml (769 B at 0.4 KB/sec)
Uploading: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/maven-metadata.xml
Uploaded: https://repository-jameselsey.forge.cloudbees.com/snapshot/tess-two/tess-two/maven-metadata.xml (276 B at 0.2 KB/sec)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 49.874s
[INFO] Finished at: Fri Jul 20 13:02:24 BST 2012
[INFO] Final Memory: 16M/81M
[INFO] ------------------------------------------------------------------------
```

The same should work for releases, just alter the repository in the pom to point to the releases repo. This isn’t particularly CloudBees specific either, it should work on any remote maven repository, I just like CloudBees, and no, I don’t work for them :P

Any comments/suggestions, please shout!