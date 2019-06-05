---
layout: post
title: 'Run Your Own Home Security Cameras'
date: '2016-03-01'
author: Kevin L
thumbnail: '/images/post_securitycam/motion-logo.png'
tags:
- security
categories:
- Project
---

I bought a Fujikam from Amazon. $100. It was laggy, noisy, and very difficult to download video logs. No email alerts. Push alerts to the app only worked when the app was open.   
I could go on about the features, but I think the security about the thing was worse...

All communications were clear text to some server in China owned by "Shenzhen Mining". *Awesome*.  
Not only that, but the thing was full of hardcoded backdoors. Meaning default admin passwords that cannot be changed.   
And this thing was internet accessible! It's currently the *best selling* IP camera on Amazon.  

[18 brands of security cameras are susceptible to easy hack](http://www.extremetech.com/extreme/147064-18-brands-of-security-cameras-are-susceptible-to-easy-hack)  
[hacks to turn your wireless ip surveillance cameras against you](http://www.networkworld.com/article/2224469/microsoft subnet/hacks-to-turn-your-wireless-ip-surveillance-cameras-against-you.html)  
[thinking of buying a security camera read this first](http://www.infoworld.com/article/2851854/security/thinking-of-buying-a-security-camera-read-this-first.html)  
[how i hacked my ip camera](http://jumpespjump.blogspot.com/2015/09/how-i-hacked-my-ip-camera-and-found.html)  
[security camera systems critically vulnerable to attackers](http://it.slashdot.org/story/13/01/29/0111238/58000-security-camera-systems-critically-vulnerable-to-attackers)  
[shodan-webcam](https://www.shodan.io/explore/tag/webcam)  

The alternatives? Pay a monthly fee for a service like Nest (dropcam) or dlink?  
Do you even trust a closed-source constantly running camera sending information *to the "cloud"*?  
I didn't think so.

Most mainstream DIY surveillance software sucks. They are usually paid software for servers running windows and IP cameras.  

[MotionEye](https://github.com/ccrisan/motioneye) is a python program that can use webcams and a computer to become a security camera with full recording and alerts.  

[There is even a version for Raspberry Pi!](https://github.com/ccrisan/motioneyeos)  


<img src="https://github.com/ccrisan/motioneyeos/wiki/images/settings-panel.png" alt="Settings" style="width: 600px;"/>  
[Checkout the wiki for more pictures](https://github.com/ccrisan/motioneyeos/wiki/Screenshots)


It's simple to install...

 - Get a [raspberry pi](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/), attach a usb webcam or [camera module](http://www.amazon.com/Raspberry-5MP-Camera-Board-Module/dp/B00E1GGE40/). (or get an [all in one kit](http://www.amazon.com/Module--Edimax-Adapter--20-Guide--Clear-Supply--Kingston-Adapter--HDMI/dp/B010MMPZCS/))
 - [Download and extract motioneyeos](https://github.com/ccrisan/motioneyeos/releases)
 - [Install motioneyeos image!](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md)
 - Log into the web interface from a laptop
 - Configure camera recording settings

![Camera](/images/post_securitycam/motioneye-pi.jpg)

I have mine saving recordings to my nas. I definitely recommend saving recordings to somewhere other than the pi.  

A cool feature is that you can cluster these together to get full coverage.  
<img src="https://github.com/ccrisan/motioneyeos/wiki/images/scenario-multiple-devices-with-a-hub.png" style="width: 600px;">


So to avoid buyer's remorse and your privacy being risked, checkout motioneyeos.


[motioneyeos wiki](https://github.com/ccrisan/motioneyeos/wiki/Configuration)
