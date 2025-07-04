---
title: Let AI handle my phone calls using bluetooth
date: 2025-4-7
draft: false
tags:
    - raspberrypi4
    - bluetooth
    - ai
    - call automation
---

#### Building What I Want — As Always
So, I’ve wanted this to build this for a long time. And I tried so many times, but couldn’t — because of Android (yes Android, I mean the android security for calls). The problem is that you have no control over the microphone or audio going throughout the call. Sure, you can hang up or place a call. You can’t also create virtual sinks or sources in android like usually we do in Linux.

But one most interesting thing is Google always listen your calls for, so what they call **advertising purposes** ✌🏼, yes I tested it. Anyway, I don’t know how they do it, and in which corner they put these policy. Just for fun I also `git clone` **AOSP (Android Open Source Project)** on my PC and it installed all libraries and that was around 300+GB. I view the code, gain some and exit.

Yes, you can also use [twillio](https://www.twillio.com/) or [sinch](https://sinch.com/) like services & directly use their API, But I wanna use some cheap on-device, full access and just can’t afford it continuously for years.

Also, In **India** [truecaller](https://truecaller.com/) provides AI Assistant features which will talk on behalf of you to spammers and may ring or hang on the call. Good feature but still they collaborated with telecom companies to use their API. Again we can’t afford that.

I tried 40+ times again & again in different ways gain more access, that’s when I found about call-screening but only for system apps and the default dialer app. After making a simple custom dialer, I got a lot of permission related to calls. But, still I can’t control audio/mic.

I am more interested in hardware than software, I worked with electronics and IoT’s. So, I’ve got an idea, Let’s take out the part of android we don’t have access to. While you are on a call and connected to a headphone, You can receive audio from speakers and send though microphone & that’s all I want.

And yes, I was also trying this but, I just realized, we can simply send data to an Bluetooth IoT module as it act's like a headphone. But none of them worked, I tried many bluetooth modules. But, in Bluetooth there are profiles, like hands-free profile **HFP, HDP, HSP.** which are mostly not available on IoT. But now I have got an `raspberrypi 4B` Model with 4 GB RAM, A whole tiny Linux machine. Has full support over all Bluetooth profiles, and I can write whole code on it. So, now I have to definitely do this.

Now, I almost got everything I need and it worked for me. So what I’ve done is set the Bluetooth profile of `raspberrypi 4` to act like a headphone for my android device. And only enabled the Phone calls option and disabled the Media Sharing on my android device for that **raspberrypi 4**. That’s not it, now my phone is using **raspberrypi 4** microphone input as call’s input and **raspberrypi 4** output as call’s output. I learned about `virtual sinks` and `virtual sources` on Linux.

Here’s the diagram explaining virtual sinks and sources, that’s how they loop-back and work.
!![Image Description](/images/Pasted%20image%2020250704222553.png)
So, I have to first create a virtual mic, you can create one with this command:
```
pactl load-module module-null-sink sink_name=VirtualMic sink_properties=device.description=VirtualMic

```
So, when you create a virtual mic (source) using pulseaudio audio server, it will also create a **Null Sink** (Blank Virtual Output Device) because how will you tell what to play in the virtual mic you created? you have to play audio into the Null Sink simply changing the output and it will loopback and go to the created linked source. And I set the default mic (source) to the virtual mic using `pactl set-default-source VirtualMic.monitor` Now playing some audio and getting a call on my connected phone will play the same audio to the incomming/outgoing call. And recording audio is so easy, you can also create a VIrtual speaker so that it won’t play in the speaker. Now, you can get the input and can send output.
There are a lot of models availabe on [huggingface](https://huggingface.com/) or directly use their spaces to use **TTS (text-to-speech) or STT (speech-to-text)** features. And it seems all done, right?

No, we have something left. How did your AI or the script knows whose call is these? The call details? How will the receive or hang up calls? I don’t want my AI to talk like a chatbot. No, I don’t want that.

There is something knows as **AT commands** inbuilt in bluetooth core. that’s how bluetooth communicate between devices to send commands between them. Commands are available according to the profiles they have. So, I tried to listen for AT commands but I can only see it in terminal I can't access it in structured format. And I decided to create an application to manually send commands over `RFCOMM` in bluetooth, so I added the bluetooth service inside my own android app (I developed an app for myself whatever I need I just add it in). I created a whole bluetooth server, retry mechanism and write commands, lot of mess for with permission in android, really it is too dificult to do things with calling, I can't access phoneNumber, i want to send it though bluetooth the the python RFCOMM server running on raspberry pi 4, So, I used a deprecated feature of android and it works, everything just works, but crashes few times.

But, now I am retrying the mechanism to listen AT commands directly and I don't have to build the android app. Just trying, here ofono is helping me but let's see what happen. Will update when completed.