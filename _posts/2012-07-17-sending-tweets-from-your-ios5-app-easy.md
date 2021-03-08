---
title: 'Sending Tweets from your iOS5 app, easy!'
date: '2012-07-17T21:41:18+10:00'
permalink: /sending-tweets-from-your-ios5-app-easy
author: 'James Elsey'
category:
    - iOS
tag:
    - facebook
    - integration
    - ios
    - ios5
    - twitter
---
Sending a tweet from your iOS application could not be any easier, Apple and Twitter really were looking out for their developers.

With iOS 5, the twitter account is authenticated under the settings menu of the device, which means that any application can request this account to use for tweet, and that is all you need to do; sign in, then request these details in your application.

Follow these easy steps

Sign into twitter on your phone
-------------------------------

Go to Settings > Twitter > Sign in, as displayed below  
![Screen Shot 2012-07-16 at 16.38.28](/assets/post_images/2012/Screen-Shot-2012-07-16-at-16.38.28.png)

Enabling tweets from your application
-------------------------------------

Then from your application, make sure that you add the twitter framework in as a linked framework. You can do this by clicking the application target, select summary page, scroll down to “Linked Frameworks and Libraries”, then add a new one, searching for “Twitter”, this all comes bundled with the Xcode development environment.

![Screen Shot 2012-07-16 at 16.34.57](/assets/post_images/2012/Screen-Shot-2012-07-16-at-16.34.57.png)

![Screen Shot 2012-07-16 at 16.34.44](/assets/post_images/2012/Screen-Shot-2012-07-16-at-16.34.44.png)

One the framework is linked, you can now import the following header into your application, such as in any one of your ViewControllers:

```objc
#import <Twitter/Twitter.h>
```

Now, we just have to display the view for allowing the user to create a tweet. I usually append this onto a button click, but you could invoke it from any other event, such as the view appearing, a slider being altered, or even after a segue. This is my example for creating a tweet on a button click :

```objc
- (IBAction)postToTwitterClicked:(id)sender 
{
    if ([TWTweetComposeViewController canSendTweet])
    {
        TWTweetComposeViewController *tweetSheet = [[TWTweetComposeViewController alloc]init];
        
        [tweetSheet setInitialText:@"This is a sample tweet!"];
        [tweetSheet addURL:[NSURL URLWithString:@"http://www.Twitter.com"]];
        
        [self presentModalViewController:tweetSheet animated:YES];
    }
    else 
    {
        UIAlertView *av = [[UIAlertView alloc] initWithTitle:@"Unable to tweet"
                                                     message:@"Please ensure that you have at least one twitter account setup and have internet connectivity. You can setup a twitter account in the iOS Settings > Twitter > login."
                                                    delegate:self 
                                           cancelButtonTitle:@"OK" 
                                           otherButtonTitles:nil];
        [av show]; 
    }
}
```

A little explanation of the above. First, we want to check if we have the capability of sending a tweet, this just checks to see that you have at least one account signed in. If you can’t send a tweet, do something to notify the user what is wrong, such as displaying an alert prompting them to sign in, otherwise the user will wonder why they can’t make a tweet.

Next, alloc/init a TWTweetComposeViewController, this is the controller that handles composing a tweet. You can set the initial message (the tweet contents). You can also set URLs, locations, and images, refer to the [documentation](http://developer.apple.com/library/ios/#DOCUMENTATION/Twitter/Reference/TWTweetSheetViewControllerClassRef/Reference/Reference.html) for info on those.

Finally, present the view controller modally (sits on top of anything else). It should look a little like this :

![Screen Shot 2012-07-16 at 16.40.56](/assets/post_images/2012/Screen-Shot-2012-07-16-at-16.40.56.png)

Once you’ve sent a tweet, it will sound a bird chirp to let the user know it is successful.

Easy, its a little shame that the same cannot be said for Facebook…hopefully [iOS6](http://www.apple.com/ios/ios6/#facebook) may ease integration.