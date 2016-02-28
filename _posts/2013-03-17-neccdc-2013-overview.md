---
layout: post
title: NECCDC 2013 Overview
date: '2013-03-17T22:46:00.000-07:00'
author: Kevin L
thumbnail: /images/post_neccdc2013/thumb_NECCDC_Team_Room.jpg
---
This year the North East Collegiate Cyber Defense Competition (NECCDC) was held in Orono, Maine. Ten different schools were allowed to attend after the qualifying round. The competition consists of four different teams: The Blue team are the schools competing, The Red team are the attackers, The White team is the support/scoring, and The Black team takes care of the hardware and designed the network. The White team will enter the room with "injects" for a team to complete. These injects are various tasks that a real sysadmin may have to do in a real environment. Here is an official description taken from the National CCDC website:

>"You have just been hired as the network and security administrators at a small company and will be taking administrative control of all information systems. You know very little about the network, what security level has been maintained, or what software has been installed. You have a limited time frame to familiarize yourself with the network and systems and to begin the security updates and patches before the red team starts actively attacking your company. In the midst of all the commotion, you have to keep up with the needs of the business and user demands while maintaining service level agreements for all critical Internet services. Welcome to the first day of the National Collegiate Cyber Defense Competition (CCDC).

CCDC is a three day event and the first competition that specifically focuses on the operational aspect of managing and protecting an existing "commercial" network infrastructure. Not only do students get a chance to test their knowledge in an operational environment, they will also get a chance to network with industry professionals who are always on the lookout for up and coming engineers. &nbsp;CCDC provides a unique opportunity for students and industry professionals to interact and discuss many of the security and operational challenges the students will soon face as they enter the job market." ([http://nationalccdc.org][1])

I was part of the Champlain College team. It was my first time attending this event and I was the youngest on the team of eight. We prepared months ahead of time for this event and even staged practice competitions in our lab. We each debated on different strategies upon entering the room. We decided on each of us focusing on a service, and learning as much as we could about it. Http, Https, Ftp, Dns, Pop3, Smtp, AD, and Infrastructure. We printed out configs and cheat sheets &nbsp;and put them into binders to walk in with. The rules specifically state that you are not allowed to bring in anything electronic or host code on a website for quick download. Only printed materials were allowed. You cannot unplug your machine or the router from the network. I was given the clients to work on, which meant desktop operating systems such as Windows xp/7/8 and Ubuntu desktop. I spent most of my time learning how to lock each down with firewalls and only granting access when needed.

![][2]  
Champlain College's Room at NECCDC  


At the competition, I personally was not fully prepared for what the red team was going to dish out at us. We walked in and machines were already broken into. Passwords that were given to us were not working and odd processes were running. We threw up our firewalls, changed passwords, and started damage assessment. Scripts were found on the web server to join it to an IRC botnet. There was also a cron job set to email /etc/shadow and other info to the attacker. The windows boxes had some vulnerable programs installed, including an old version of both IE and OpenOffice. After clearing through the damage as best as we could, it was time to start hardening our services and system. I focused on uninstalling anything that was unnecessary and configuring the firewalls on the machines. My teammates spent their time doing the same and rewriting the configurations on their services. The injects started to come in, and they kept coming. Setup a secure wireless with guest ssid, configure openvpn, install and maintain Nagios, configure a physical firewall, etc...

We left the room with an assignment to perform a vulnerability assessment on our network and write an executive summary about it to our CEO. That night we wrote and printed more configs to prepare for the next day.   
Saturday was a ten hour non-stop day in the competition. It seemed to just be a day of chaos and injects. The room was never quiet. One of the injects was for the white team member in the room to simulate a typical user and browse the internet. He was guided to a malicious website ran by the red team, and prompted to install java and a bunch of applets. This took out the windows 8 client and a windows xp client. It was difficult to assess the damage that this did, so these computers were vlan'ed off the network while I removed most of the infection.

![][3]  
The Red Team's plan of attack for the three-day event.

The last day was only a few hours long and was a race to see what team could rack up the most points before the finish. The red team seemed like they were throwing everything they had at us.  They took control of a Windows XP box and starting typing on the screen for us. Our firewalls were going crazy as they tried to Dos-attack us through any vector they could.   

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; This year, the Red Team made something special for us Blue Teams to try at the end of the competition. It was a hacking challenge for us to capture a flag placed on a box running a webserver. The flag could be anywhere on the system. We were only given an hour to complete the task, and we could use whatever tools we could get our hands on. Our team actually ended up winning the challenge! We were rewarded with a Pwn Plug Elite by Pwnie Express (<http: pwnieexpress.com="" products="" pwnplug-elite="">). The Red Team told us that we captured the flag in under a minute from the start of the connection to viewing the flag.txt file.   
The steps we took to completing the challenge:   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1. Found a spot on the website to upload files. Attempted to upload php shells with no avail.    
&nbsp;&nbsp;&nbsp; &nbsp; 2. Moved on and check out robots.txt for clues. Found a bunch of directories in the listings.   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3. Viewed the source of each, eventually finding a commented line describing a place to inject code,&nbsp;but&nbsp; was "disabled".   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4. Injected code and it worked, found the flag in the directory using linux commands.

![][4]




![][5]  
Red Team thought they were so clever

&nbsp;&nbsp;&nbsp; Overall it was a great experience that led me to meet a lot of amazing people in the field. University of Maine did a great job hosting the event. A few members of the Red Team came in and told us what they did to our network with full detailed reports. It was crazy to see how much they did and how they did it. I hope to go back next year and patch up some of the mistakes made this year. Plus I love to see what tricks the Red Team uses against us. I managed to print a few scripts out before I left.

[1]: http://nationalccdc.org/
[2]: http://www.kevinlaw.info/images/post_neccdc2013/NECCDC_Team_Room.jpg
[3]: http://www.kevinlaw.info/images/post_neccdc2013/NECCDC_RedTeam_Attack_Days.png
[4]: http://www.kevinlaw.info/images/post_neccdc2013/hacking_challenge.jpg
[5]: http://www.kevinlaw.info/images/post_neccdc2013/Hacking_Challenge_Flag.jpg
  </http:>
