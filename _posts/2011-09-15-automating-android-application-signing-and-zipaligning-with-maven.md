---
title: 'Automating android application signing and zipaligning with maven'
date: '2011-09-15T18:30:36+10:00'
permalink: /automating-android-application-signing-and-zipaligning-with-maven
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - android
    - continuous-integration
    - java
    - jayway
    - maven
    - maven-android
    - xml

---
This is something that has had me tearing my hair out for a few days now, I was pretty much border-line braveheart-ing my screen….

![Code rage](/assets/post_images/2011/braveheart.jpg)

I’ve recently been on a little drive to try to [maven-ize](http://code.google.com/p/maven-android-plugin/) my projects. All had been going well until I needed to sign and zipalign my APKs. This post will help you conquer that barrier with the use of some maven plugins.

When using ant, I was able to simply enter keystore details into build.properties and just call “ant release”. Unfortunately that approach doesn’t carry across to maven, and you have to provide some more configuration.

Firstly, before we go any further, I’m going to assume that you already have a maven android project setup, so you have a pom.xml, you’ve configured the maven-android-plugin and you can run “mvn clean install” to build your APK, and “mvn android:deploy” to deploy it to an emulator. If you haven’t got that far, I’d suggest you have a look at one of my[ previous posts](/android-continuous-integration-all-made-lovely-with-maven) to help get you up to speed.

So, when you want to build your application with maven, you’d run “mvn install”. That will, by default, use a debug key to sign your APK. When we want to build a releasable APK we still want to execute the same install goal, however we’ll want to use a proper key. Luckily, maven provides something called [profiles](http://maven.apache.org/guides/introduction/introduction-to-profiles.html).

![Sign Application](/assets/post_images/2011/Sign-Application.jpg)

In short, maven profiles allow you to still perform the same standard goals, yet they behave slightly different, in the manner of binding extra steps to them, all will come clearer in a moment.

This has already been covered, in a useful post by the guys at [Novoda](http://novoda.com/2010/08/13/android-continuous-integration-android-maven-plugin/), and various other [blog posts](http://www.hascode.com/2010/04/signing-apk-with-the-maven-jar-signer-plugin/) scattered around the web. I’ve followed at least 10 tutorials and each time I was unable to get the signing process to work correctly, each time I encountered the following error :

```
INFO] jarsigner: attempt to rename C:\android-projects\jameselsey_andsam_branch_mavenbranch\target\LanguageSelection-1.0.0-SNAPSHOT.jar to C:\android-projects\jameselsey_ands
am_branch_mavenbranch\target\LanguageSelection-1.0.0-SNAPSHOT.jar.orig failed
[INFO] ------------------------------------------------------------------------
[ERROR] BUILD ERROR
[INFO] ------------------------------------------------------------------------
[INFO] Failed executing 'cmd.exe /X /C "C:\development\tools\Java\jdk1.6.0_20\jre\..\bin\jarsigner.exe -verbose -keystore my-release-key.keystore -storepass '*****' -keypass '
*****' C:\android-projects\jameselsey_andsam_branch_mavenbranch\target\LanguageSelection-1.0.0-SNAPSHOT.jar mykeystore"' - exitcode 1
[INFO] ------------------------------------------------------------------------
```

I’d [tried everything](http://stackoverflow.com/questions/7421710/android-maven-jarsigner-jarsigner-attempt-to-rename-x-but-failed), even copying out the command and pasting into a command window worked, but I could not get it to work from maven. Unfortunately it seems there may be an issue with the maven-jarsigner-plugin (as suggested in the comments on the Novoda post). However fear not, for there is an alternative, the [maven-jar-plugin](http://maven.apache.org/plugins/maven-jar-plugin/plugin-info.html).

The [maven-jar-plugin](http://maven.apache.org/plugins/maven-jar-plugin/plugin-info.html) is a similar plugin to the jarsigner plugin, however the signing APIs are now deprecated and it points you to use the (apparently) broken maven-jarsigner-plugin. Using a deprecated API doesn’t particulary concern me in this instance, as its just signing artefacts.

Take a look at my profiles section, copy this into your pom.xml :

```xml
<profiles>
<profile><!-- release profile. uses keystore defined in keystore.* properties. signs and zipaligns the app to the target folder-->
            <id>release</id>
            <activation>
                <activeByDefault>false</activeByDefault>
            </activation>
            <build>
                <defaultGoal>install</defaultGoal>
                <finalName>${project.artifactId}-${project.version}</finalName>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-jar-plugin</artifactId>
                        <version>2.2</version>
                        <executions>
                            <execution>
                                <id>signing</id>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                                <phase>package</phase>
                                <inherited>true</inherited>

                                <configuration>
                                    <keystore>
                                        my-release-key.keystore
                                    </keystore>
                                    <storepass>mypassword</storepass>
                                    <keypass>mypassword</keypass>
                                    <alias>mykeystore</alias>
                                    <verbose>true</verbose>
                                    <verify>true</verify>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>

                    <plugin>
                        <groupId>com.jayway.maven.plugins.android.generation2</groupId>
                        <artifactId>maven-android-plugin</artifactId>
                        <inherited>true</inherited>
                        <configuration>
                            <sign>
                                <debug>false</debug>
                            </sign>
                        </configuration>
                    </plugin>

                    <plugin>
                        <groupId>com.jayway.maven.plugins.android.generation2</groupId>
                        <artifactId>maven-android-plugin</artifactId>
                        <configuration>
                            <zipalign>
                                <verbose>true</verbose>
                                <skip>false</skip>
                                <!-- defaults to true -->
                                <inputApk>${project.build.directory}/${project.artifactId}-${project.version}.apk</inputApk>
                                <outputApk>${project.build.directory}/${project.artifactId}-${project.version}-RELEASE.apk
                                </outputApk>
                            </zipalign>

                        </configuration>
                        <executions>
                            <execution>
                                <id>zipalign</id>
                                <phase>install</phase>
                                <goals>
                                    <goal>zipalign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
</profiles>
```

OK, so lets walk through what the above profile does. Firstly, the “id” of the profile is “release”, so when you want to apply this profile, you’d run “mvn install -Prelease”. The “activeByDefault” is set to false, which means you need to use the above arguement, if you flip that over, you won’t need the P flag.

The execution goal is to “sign”, which is the API on the plugin, [documented here](http://maven.apache.org/plugins/maven-jar-plugin/sign-mojo.html). We bind this execution on to the standard “package” goal. Then we provide the configuration elements which specify the details of the keystore.

In pure English, this binds the plugin phase onto the package goal of maven, so anytime we run using this profile it’ll execute the jar signing of our artefacts. The “verify” tag gives us extra piece of mind by verifying that the signing was successful afterwards.

I’ve also setup a zipalign profile that will take the APK, zipalign it, and rename it to “\*-RELEASE.apk”, so I know that particular APK is the one which I’ll release to market.

So thats it, once more a success, and its just one small step on the ladder to android glory!

![funny-pictures-kitten-climbs-the-ladder-of-success1](/assets/post_images/2011/funny-pictures-kitten-climbs-the-ladder-of-success1.jpg)

Phhew!! Done, finally I was able to shutdown the laptop and go to sleep at 3am….on a school night!!!

Good luck, feel free to comment!

Peace.