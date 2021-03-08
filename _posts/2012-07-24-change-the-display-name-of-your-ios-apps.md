---
title: 'Change the display name of your iOS apps'
date: '2012-07-24T21:01:52+10:00'
permalink: /change-the-display-name-of-your-ios-apps
author: 'James Elsey'
category:
    - iOS
tag:
    - ios
    - ios5
---
Decided on a long name for your application when you created it, but are now fed up of seeing it display like this on the home screen?

![Screen Shot 2012-07-16 at 17.13.04](/assets/post_images/2012/Screen-Shot-2012-07-16-at-17.13.04.png)

You can quickly and easily change the display name of your application but amending InfoPlist.strings to the following

```
/* Localized versions of Info.plist keys */

CFBundleDisplayName = "My App";
```

Easy as that, since the plist file is localised, we can use this for multiplate languages/locales.