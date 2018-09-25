---
layout: post
title: 'pyWebcamSteg: An annoymising proxy through your webcam'
bannerImg: /assets/img/pyWebcamSteg/banner.jpg
featured: false
tags: Security
theme: '#fe7855'
---	
ExploitDB's [Google Hacking Database (GHDB)](https://www.exploit-db.com/google-hacking-database/) is a great resource for finding sneaky search queries for Google which lets you find public things on the web which probably shouldn't be public

One search query which really stuck out to me was `intitle:webcam 7 inurl:8080 -intext:8080` which shows you simple pages for public webcams:

![webcam7](/assets/img/pyWebcamSteg/webcam7.png)

Many of these are office buildings clearly not meant to be publicaly viewable (A personal favourite of mine is the Anchorage Moose Cam)

This seemed like a perfect side channel to pass information!

# Steganography
Steganography is the art of hiding secret data inside some carrier file without changing it’s appearance to the casual observer. The carrier file is typically an image as pixel values can be permuted slightly and still appear identical to the casual observer.

A very simple way of doing this relies on modifying the least significant bit of each pixel to be that of your data. Changing this bit modifies the pixel value by at most 1, so is inperceptable to the human eye on a full colour image.

![webcam7](/assets/img/pyWebcamSteg/stegExplain.svg)

There are loads of algorithms extend this idea in more complicated ways- JSTEG, F5, OUTGUESS to name just a few. They all have varying features such as the amount of information you can encode, how detectable is it, what image files it works on and how it can survive compression.

Many of these can be detected using statistical analysis, but the key with steg is to avoid raising suspicion in the first place!

# Back to webcams
So how does this relate to webcams? Well the webcam7 websites transmit video in a very simplistic way using mJPEG - each frame is simply a jpeg image. And each of these images we can sneak some information in using steganography!

![system1](/assets/img/pyWebcamSteg/system1.svg)

The system has 2 components:

- **Server** - the server pretends to be a webcam7 server (has the same directory structure and should ideally pretend to be the same hardware). This operates exactly as a webcam7 server would, but encodes the secret message using steganography
- **Client** - the client browses to the site hosted by the server, downloads the images and extracts the secret message.


A key question remains - **how does the server know the client is listening?** A simple solution to use the camera control buttons. When a client is ready it sends a specific set of key combinations (UP,DOWN,LEFT,RIGHT,ZOOM etc.), which the server recieves and knows a client is listening.

## A webcam proxy

We now have a method of sending information from one place (the server) to another (the client). Wouldn't it be fun if this was bidirectional? Wouldn't it be even more fun if you could browse the internet entierly through webcam streams!

When you navigate to your favourite website over the internet, a series of messages are passed. First your internet browser forms a **request** detailing the page you want, any form data, the method etc. This is sent to the server which hosts the side which forms a **response** containing the page you want and is sent back to you to be displayed. This request/response idea is key to the world wide web.
![www](/assets/img/pyWebcamSteg/www.svg)

Using the webcam steganography approach from earlier we can hide both the request and response in webcam streams. Both the client and the server host a webcam7 page. When the client wants to visit a page, they encode the request in their own webcam stream and 'poke' the server to let it know there is a request waiting. The server grabs the images, decodes them to get the request and processes it. The server can then go and request the webpage on the client's behalf, before encoding the response in their own webcam stream to reverse the process.

![system2](/assets/img/pyWebcamSteg/system2.svg)

This system allows the request from the client to be hidden in a way which doesn't arouse suspicion like a traditional VPN.

---
The code for this project can be found at on [Github](https://github.com/ghomasHudson/pyWebcamSteg).

