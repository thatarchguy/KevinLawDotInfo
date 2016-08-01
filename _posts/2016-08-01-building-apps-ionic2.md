---
layout: post
title: 'Building Mobile Apps with Ionic 2'
date: '2016-08-01'
author: Kevin L
thumbnail: '/images/post_building_apps/ionic-logo.png'
---

## Preface

The only mobile application development experience I had was using Visual Studio to write an app for my old Blackjack II running Windows Mobile 6.0. (I don't even think "app" was a common word back then.)

After a lot of research, I decided to _not_ go with writing native applications. A lot of work has been done with these hybrid frameworks that use html, js, and css to create applications that work on all platforms.

A common misconception is that these frameworks do not grant you access to native APIs.  
[Look at all the native access Ionic has!](http://ionicframework.com/docs/v2/native/)

```
import { Vibration } from 'ionic-native';

// Vibrate the device for a second
Vibration.vibrate(1000);

```


## Ionic 2

![demo gif](http://ionicframework.com/img/blog/popover-preview.gif)

Ionic is a framework that uses Apache's Cordova project underneath to create a cross-compatible mobile application.

>Think of Ionic as the front-end UI framework that handles all of the look and feel and UI interactions your app needs in order to be compelling. Kind of like “Bootstrap for Native,” but with support for a broad range of common native mobile components, slick animations, and beautiful design
[...]
Since Ionic is an HTML5 framework, it needs a native wrapper like Cordova or PhoneGap in order to run as a native app.


Ionic is opinionated. It sets up a lot of the boilerplate with `ionic start MyNewApp --v2`

The technologies used in Ionic 2 are Cordova, Sass, Typescript, and Angular2.

A comparison of the new version:

| Ionic 1    | Ionic 2    |
|------------|------------|
| Angular 1  | Angular 2  |
| Javascript | Typescript |
| Sass       | Sass       |

For someone who is not familiar with Angular (like myself), the learning curve is quite steep.

Testing the application is quite easy:  
`ionic serve --lab` will bring up multiple views for supported platforms

`ionic run android` or `ionic run ios` to test on a device or emulator.

Since these apps are just webapps, they can be ran in the browser natively.  
Here is a solid example of an Ionic 2 application in the browser:  
[http://coenraets.org/apps/ionic2-realty/](http://coenraets.org/apps/ionic2-realty/).
## Learning

I actually didn't know Ionic 2 was a thing until I finished a few tutorials for Ionic 1.

Desipte still using Ionic 1, I highly recommend [appcamp.io](http://appcamp.io/) to learn the basics.


**Learn Angular2 first!**

Definitely become familar with Angular2 and Typescript before attempting to write an Ionic application. I ended up learning all 3 at once. While it was an interesting experience, I don't recommend it. However, Ionic came with boilerplate that included a gulpfile to compile the typescript and Sass for me which was nice.  

_This is more about learning Angular2 & Typescript than learning Ionic_


Awesome Tutorials:

 - - [Angular2 w/ Typescript](https://angular.io/docs/ts/latest/tutorial/)  
 - - [Ionic 2](http://ionicframework.com/docs/v2/getting-started/tutorial/)

If you are bad at designing interfaces like I am, you can checkout ionics intuitive drag and drop UI editor called [Ionic Creator](http://ionic.io/products/creator).

![ionic creator](http://ionicframework.com.s3.amazonaws.com/blog/creator/creator-header.png)
## Coding

I ended up writing a simple application that plays a noise on click:  
[https://github.com/thatarchguy/Rastahorn-Ionic2](https://github.com/thatarchguy/Rastahorn-Ionic2)

I'll be adding more features to the app as I continue development.

It was much easier to write this application than it was to learn the Android SDK, Java, iOS SDK, and Swift.

I can plug in my android phone and have it test right on the device using `ionic run android --device`
