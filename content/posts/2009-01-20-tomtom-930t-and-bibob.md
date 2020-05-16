---
categories:
- Tech Stuff
date: "2009-01-20T22:47:11Z"
meta:
  _edit_last: "1"
status: publish
tags:
- 930T
- bibob
- gprs
- TomTom
title: TomTom 930T and Bibob
---
I just got my new TomTom 930T GPS, I unpacked it and connected it to my phone via Bluetooth, it asked me if I wanted to connect to "internet" and of course I had to try it, it asked me which country I was in, then which phone I had, my Nokia E65 was in the list, then which telco I used... But to my luck mine was not in the list, I know my carrier Bibob uses Sonofon network, so I decided to try choosing that from the menu.. but with no result, then I tried to configure it manually... Still not working.. Then I remebered I wrote a post a while back about getting the phone to work as a modem, back then I figured out that I had to change the connection string to make it work..

I found my old post, typed in the connection string on the TomTom lo and behold... It connected right away... So if you want to connect you TomTom device to you mobile with a Bibob subscription, you will have to put in the following connection string : at+CGDCONT=1,"IP","Internet.bibob.dk"

