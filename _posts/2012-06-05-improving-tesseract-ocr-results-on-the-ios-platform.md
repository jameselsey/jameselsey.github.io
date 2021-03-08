---
title: 'Improving Tesseract OCR results on the iOS platform'
date: '2012-06-05T21:30:58+10:00'
permalink: /improving-tesseract-ocr-results-on-the-ios-platform
author: 'James Elsey'
category:
    - iOS
tag:
    - ios
    - iphone
    - objective-c
    - ocr
    - tesseract
---
If you’ve found yourself using Tesseract on the iOS platform, and you’re scratching your head as to why the OCR results are so terribly incorrect, you might be interested in the following. Most of the tesseract iOS tutorials talk about compiling the libraries, but don’t really cover how to use it.

![funny-pictures-cat-has-an-iphone](/assets/post_images/2012/funny-pictures-cat-has-an-iphone.jpeg)Theres always an app for that, but how do you understand how it works?

Are you using something like this to interface with the tesseract API?

```objc
char* text = tess->TesseractRect(imageData,(int)bytes_per_pixel,(int)bytes_per_line, 0, 0,(int) imageSize.height,(int) imageSize.width);

NSLog(@"Converted text: %@",[NSStringstringWithCString:text encoding:NSUTF8StringEncoding]);
```

I was using this to start with, and the results were terrible, if it was able to read anything it was mostly returning special characters or just utter nonsense.

Looking closer at the API documentation, you can see this :

```cpp
/**
   * Recognize a rectangle from an image and return the result as a string.
   * May be called many times for a single Init.
   * Currently has no error checking.
   * Greyscale of 8 and color of 24 or 32 bits per pixel may be given.
   * Palette color images will not work properly and must be converted to
   * 24 bit.
   * Binary images of 1 bit per pixel may also be given but they must be
   * byte packed with the MSB of the first byte being the first pixel, and a
   * 1 represents WHITE. For binary images set bytes_per_pixel=0.
   * The recognized text is returned as a char* which is coded
   * as UTF8 and must be freed with the delete [] operator.
   *
   * Note that TesseractRect is the simplified convenience interface.
   * For advanced uses, use SetImage, (optionally) SetRectangle, Recognize,
   * and one or more of the Get*Text functions below.
   */
  char* TesseractRect(const unsigned char* imagedata,
                      int bytes_per_pixel, int bytes_per_line,
                      int left, int top, int width, int height);

```

Therefore, swap over and use this implementation:

```objc
    tess->SetImage(imageData,(int) imageSize.width, imageSize.height, (int)bytes_per_pixel,(int)bytes_per_line);
    char* someChars = tess->GetUTF8Text();
    NSString * someString = [NSString stringWithCString:someChars encoding:NSUTF8StringEncoding];
    NSLog(@"Better results this way %@", someString);
```

Nothing groundbreaking here, just pointing it out!

Don’t forget to use blacklisting and whitelisting for character sets, that helps improve results tremendously.

Thanks