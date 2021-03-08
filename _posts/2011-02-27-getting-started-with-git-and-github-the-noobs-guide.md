---
title: 'Getting started with Git, and GitHub, the noobs guide'
date: '2011-02-27T12:43:53+10:00'
permalink: /getting-started-with-git-and-github-the-noobs-guide
author: 'James Elsey'
category:
    - Git
tag:
    - developer-tools
    - git
    - github
    - scm
---
I’ve always been a Subversion man myself, but today I took Vaders’ advice and joined the darkside. As part of a tutorial I wrote on here I wanted to host some code on Github for people to download, so I had to learn some basic Git usage, heres how to get started.

First thing, head over to Github and get yourself an account, it only takes about 5 minutes. After that, create a *repository*. You can have as many public repositories as you want using a free account, so don’t worry about wasting any of your allowance. Think of a repository as a project, for each of my android applications that I have hosted I have them in their own repository, you could easily have them all in one, but its a lot neater to keep them separate, and gives you the choice to have a wiki or issue tracking page separate across the repositories.

After you’re all setup on Github, go ahead and download the client [here](http://git-scm.com/download), then we’ll need to setup a few things on your development environment.

Setting up a Git SSH key
------------------------

You’ll need to setup an SSH key in order to commit anything to Github, open up Git Bash. You can find this under Start > Programs > Git > Git Bash.

Once you’ve got the Git Bash console open, run the following to generate a key:

```
James@NEVADA ~/.ssh
$ ssh-keygen -t rsa -C "james.elsey@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/James/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/James/.ssh/id_rsa.
Your public key has been saved in /c/Users/James/.ssh/id_rsa.pub.
The key fingerprint is:
85:70:7f:d1:3d:94:c1:89:9f:36:b4:9d:04:81:ec:bd james.elsey@gmail.com
```

This will generate a key into the file id_rsa.pub, so if you run the following you can see what that key is :

```
cat id_rsa.pub
```

Copy the key value, then go back to Github, click on Account settings and then add a public key, using that value in the big box.

After thats done, we’re ready to start checking your code into your Github repository.

Create a project, then check it into Github
-------------------------------------------

I’m going to assume you’ve already created a dummy application on your environment that you want to commit to Github. This can be Android, J2EE or anything you want, but for the purposes of this demo I’ve created an android application.

Open up a command prompt and cd into the root directory of your project. Then run the following :

```
# Initialize the local Git repository
git init
# Add all (files and directory) to the Git repository
git add .
# Make a commit of your file to the local repository
git commit -m "First check-in."
# Show the log file
git log
```

What this does is add and commit your files under the current directory (so thats your entire project) into a local Git repository on your local disk. Thats great, but if your PC fails you’ll lose your work, so we need to get that pushed up onto Github, use the following to do so:

```
C:\development\projects\TextToSpeechDemo>git push -u origin master
Enter passphrase for key '/c/Users/James/.ssh/id_rsa':
Counting objects: 47, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (30/30), done.
Writing objects: 100% (47/47), 20.41 KiB, done.
Total 47 (delta 3), reused 0 (delta 0)
To git@github.com:jameselsey/TextToSpeechDemo.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

What this does is push all from origin into master, master is a bit like trunk in subversion terms.

So thats it, in 10 minutes you’ve been able to setup a Github account, setup an SSH key, create a repository, commit and push your code into your Github repository.

You can also create a few branches, commit into those and push out into their own branches on Github, like I have done here :

```
C:\development\projects\TextToSpeechDemo>git branch
* master

C:\development\projects\TextToSpeechDemo>git branch-a
git: 'branch-a' is not a git command. See 'git --help'.

C:\development\projects\TextToSpeechDemo>git branch -a
* master
  remotes/origin/master

C:\development\projects\TextToSpeechDemo>git branch TextToSpeechDemoStaticSpeech

C:\development\projects\TextToSpeechDemo>git checkout TextToSpeechDemoStaticSpeech
M       .idea/workspace.xml
Switched to branch 'TextToSpeechDemoStaticSpeech'

C:\development\projects\TextToSpeechDemo>dir
 Volume in drive C has no label.
 Volume Serial Number is 5EF1-749D

 Directory of C:\development\projects\TextToSpeechDemo

26/02/2011  13:28    <DIR>          .
26/02/2011  13:28    <DIR>          ..
26/02/2011  13:43    <DIR>          .idea
26/02/2011  13:28               691 AndroidManifest.xml
26/02/2011  12:21    <DIR>          assets
26/02/2011  12:21    <DIR>          bin
26/02/2011  12:21               696 build.properties
26/02/2011  12:21             3,294 build.xml
26/02/2011  12:21               362 default.properties
26/02/2011  12:21    <DIR>          gen
26/02/2011  12:21    <DIR>          libs
26/02/2011  12:21               447 local.properties
26/02/2011  13:28    <DIR>          out
26/02/2011  12:21             1,195 proguard.cfg
26/02/2011  12:21    <DIR>          res
26/02/2011  12:21    <DIR>          src
26/02/2011  12:21             1,952 TextToSpeechDemo.iml
               7 File(s)          8,637 bytes
              10 Dir(s)  33,205,288,960 bytes free

C:\development\projects\TextToSpeechDemo>git commit -a -m "Moved static speech demo into separate branch to keep it"
[TextToSpeechDemoStaticSpeech 46098c9] Moved static speech demo into separate branch to keep it
 1 files changed, 2 insertions(+), 2 deletions(-)

C:\development\projects\TextToSpeechDemo>git push -u origin TextToSpeechDemoStaticSpeech
Enter passphrase for key '/c/Users/James/.ssh/id_rsa':
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 456 bytes, done.
Total 4 (delta 3), reused 0 (delta 0)
To git@github.com:jameselsey/TextToSpeechDemo.git
 * [new branch]      TextToSpeechDemoStaticSpeech -> TextToSpeechDemoStaticSpeech
Branch TextToSpeechDemoStaticSpeech set up to track remote branch TextToSpeechDemoStaticSpeech from origin.

C:\development\projects\TextToSpeechDemo>git checkout master
Switched to branch 'master'

C:\development\projects\TextToSpeechDemo>git branch TextToSpeechDemoDynamicSpeech

C:\development\projects\TextToSpeechDemo>git branch -a
  TextToSpeechDemoDynamicSpeech
  TextToSpeechDemoStaticSpeech
* master
  remotes/origin/TextToSpeechDemoStaticSpeech
  remotes/origin/master

C:\development\projects\TextToSpeechDemo>git checkout TextToSpeechDemoDynmaicSpeech
error: pathspec 'TextToSpeechDemoDynmaicSpeech' did not match any file(s) known to git.

C:\development\projects\TextToSpeechDemo>git checkout TextToSpeechDemoDynamicSpeech
M       .idea/workspace.xml
M       gen/com/jameselsey/demo/texttospeechdemo/R.java
M       res/layout/main.xml
M       res/values/strings.xml
M       src/com/jameselsey/demo/texttospeechdemo/Main.java
Switched to branch 'TextToSpeechDemoDynamicSpeech'

C:\development\projects\TextToSpeechDemo>git commit -a -m "Added a text box for dynamic speech"
[TextToSpeechDemoDynamicSpeech 0f9ef63] Added a text box for dynamic speech
 5 files changed, 116 insertions(+), 37 deletions(-)

C:\development\projects\TextToSpeechDemo>git push -u origin TextToSpeechDemoDynamicSpeech
Enter passphrase for key '/c/Users/James/.ssh/id_rsa':
Counting objects: 38, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (9/9), done.
Writing objects: 100% (21/21), 2.40 KiB, done.
Total 21 (delta 7), reused 0 (delta 0)
To git@github.com:jameselsey/TextToSpeechDemo.git
 * [new branch]      TextToSpeechDemoDynamicSpeech -> TextToSpeechDemoDynamicSpeech
Branch TextToSpeechDemoDynamicSpeech set up to track remote branch TextToSpeechDemoDynamicSpeech from origin.

C:\development\projects\TextToSpeechDemo>git checkout master
Switched to branch 'master'
```

Hope this helps!

Further Reading
---------------

- [Setting up Git SSH keys](http://help.github.com/msysgit-key-setup/)
- [Download the Git client](http://git-scm.com/download)
- [A really good Git tutorial](http://www.vogella.de/articles/Git/article.html)