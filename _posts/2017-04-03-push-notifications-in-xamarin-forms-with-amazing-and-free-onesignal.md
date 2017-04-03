---
title: "Push notifications in Xamarin.Forms with amazing and free OneSignal"
layout: post
date: 2017-04-03
headerImage: false
tag:
- xamarin
- xamarin.forms
- c#
- push notifications
category: blog
author: yurababiy
description: Getting started with push notifications using OneSignal.
---

## Summary:
I might assume that you are looking for the way to send __push notifications__ to
__Xamarin.Forms__ or simply __Xamarin__ application. I was like you a few days ago, and now
 I have my app that works with push notifications for both __iOS__ and __Android__. Absolutely
___free___. With real-time tracking, full scalability, segmentation, automation and much
more.  
 Wonder how it could be possible? With wonderful [__OneSignal__](https://onesignal.com/)! Already
interesting? Let's do it!

## Introduction:
So how do push notifications works? Not so easy. To say simply, there are three
main things that do work. Server, that sends notifications. A regulated service,
individual for every platform, that manage really much work between server and
the third - actual device, receiver.  
Normally, you would create much code to keep this things working. But OneSignal
does all work for us.

## Starting to work with OneSignal:
It's very easy. Create an account and an app. After that, you will see our private
cabinet. You could do all this what we will do next by yourself, there are many tips
and excellent documentation, but I will help you with some steps.
_Click on App Settings_, and this is where the interesting is starting.

## Configure iOS platform:
This part was the hardest to me. I assume that you already have properly tuned your
development provision profile, id, device for debug and development
certificate for iOS. If it is so, than you in same position where I was. If not,
then I'm suprised why you are reading it, but still there is an option for you.
[This](https://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/)
may help you to set it all up. OneSignal assumes that you has it done, and
now you only want to add push notifications functionality, and it's right.
But that didn't work for me, so I was forced to recreate id and provision
profile and delete all previous, then set it up in Xcode. So, I would suggest
you do the same if it will not work for you either. Also from my experience
better to create all things manually instead of automatically.  
Okay, so after we clicked App Setting, next step is to configure our platform.
_Click Configure on iOS row_, then we need to create push certificate.
There two options: automatically and manually. I tried once automatic and had a problem
with password on the .p12 certificate, thus better create it manually.  
Don't forget to add your certificate to platform. Now, it's ___very important___
to enable push notifications for your app in Xcode. If you lost it in instruction,
you can find it [here](https://documentation.onesignal.com/docs/ios-sdk-setup#section-2-add-required-capabilities).  

__Some tips about that:__  
1. You should create this OneSignalNot.. in your current project(app).  
I missed it once and you don't want to do so;  
2. Change NotificationService.m, but not NotificationService.h;  
3. Replace `project_name` with your project name in podfile;  
4. Be sure that you have exactly same text in podfile(except project name), as it is on site;
5. Don't forget to replace default appId with your OneSignal App ID in _AppDelegate_;   

Also, be sure that proper provision profile is selected in general tab of your
project in Xcode. For sure, you can also in _Build Setting_ tab in the _Signing_
section set proper field manually.  
Now, in _App Settings_ you finally should see _Status_ of iOS platform as _Active_.

## Configure Android platform:

For Android, it's extremely simple. Click Configure for Android platform and just
follow instructions. After you done that, you should see your Android platform
status as Active.

## Setup OneSignal SDK for Xamarin

It's really great, that OneSignal SDK for Xamarin was created. [Here](https://github.com/OneSignal/OneSignal-Xamarin-SDK) is where it
live. Nuget package was created pretty recently, so we are happy that we can use
in easiest way. Here is where was conversation about it. And I thank [Martijn]
(https://github.com/martijn00) for such good work, not only with OneSignal SDK,
but with great things also.
So, follow [this](https://documentation.onesignal.com/docs/xamarin-sdk-setup) instructions.
Some tips:  
1. Don't forget to change `manifestApplicationId`. Your package name you can find
in _Android project properties -> Android Manifest -> Package Name._ If there is
no name, you should add one. Name must consist only with lower case letters and
has at least one dot. In example `.myapppackagename`. 
