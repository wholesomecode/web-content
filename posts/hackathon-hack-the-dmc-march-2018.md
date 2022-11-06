---

title: Hackathon – Hack the DMC – March 2018
post_date: 2019-01-01T23:00:00.000Z
post_status: publish

---

Hackathons (or Hack Days) are a great way to engage with technical people in your community (not to mention one or two business folks and entrepreneurs), they are also a huge amount of fun!

Over the 9th and 10th of March I attended the 24 hour hackathon [Hack the DMC](https://www.hackathon.com/event/hack-the-dmc-43709277626) at [Barnsley Digital Media Centre](http://www.barnsleydmc.co.uk/), with an aim of solving problems faced by multi-occupancy business centres.

What happened?
--------------

There were some fantastic really useful things being worked on.

Some people worked on pieces of technology that detected the amount of people using a workspace at one time using heat and CO2 sensors!

%[https://twitter.com/julies_cr8tve/status/972495816245080064]

%[https://twitter.com/julies_cr8tve/status/972505250753077249]

Another team used machine learning and email to help foster collaboration between tenants.

%[https://twitter.com/julies_cr8tve/status/972500176849002497]

[Matt Brailsford](https://twitter.com/mattbrailsford) worked on a system to allow him to ask Alexa (via the Amazon Echo) interesting business questions, such as if his packages had arrived.

%[https://twitter.com/julies_cr8tve/status/972507325700411392]

What did I work on?
-------------------

Seeing the amazing innovation in the room, [Craig Burgess](https://twitter.com/craigburgess) asked me if I fancied building a ‘Selfie Button’. A button that took selfies and posted them to Twitter!

As I had never engaged in hardware development before, I thought it would be a fantastic challenge.

Building The Selfie Button
--------------------------

After quite a while of staring at a Raspberry Pi (as this was pretty new to me), and figuring out that I had no way to connect to it and start writing code, I settled for second best, and started coding on my Mac.

Settling into our natural positions, while I got my code on, Craig started designing the logo for the product.

The language of choice for the button was [Python](https://www.python.org/). A language I had never coded in before, however I figured that it couldn’t be too difficult, and with the help of Google and several websites, I wrote a small Python application that sent a tweet on the press of a key from my Mac, using the [Tweepy Python library](https://github.com/tweepy/tweepy).

%[https://twitter.com/theselfiebutton/status/972189564159823873]

I then managed to tweet a picture of some eggs (don’t ask).

%[https://twitter.com/theselfiebutton/status/972197967389184006]

I then got it to use my Mac’s webcam to tweet out a photo.

%[https://twitter.com/theselfiebutton/status/972219378891706369]

Forgive me, I’d been at it a few hours, and the messages being posted started to get a little weird.

Hooking it up to the Pi
-----------------------

After that little victory, I decided to leave for the night. Craig kept at it, and in my absence he:

*   Hooked up the Raspberry Pi to a keyboard, mouse and monitor and got an operating system installed
*   Remoted in from his Mac to the Raspberry Pi via SSH so he could write code
*   Altered the code we had written so that it sent a tweet when a physical button had been pressed
*   Altered the code so that it used a USB webcam connected to the Raspberry Pi to take an image
*   Solved some issues whereby the webcam needed to warm-up before it could send a good quality image

The next day
------------

By the time I showed up the next morning, Craig was working on getting a speaker to play some beeps to give an indication of when the photo was being taken.

I set to work SSHing into the Pi, and made a little function to give a ‘beep, beep, beep, beeeeeeeep’ when the photo was being taken.

%[https://twitter.com/theselfiebutton/status/972430508830416896]

We needed to work out exactly the timings of when the photo was taken so we could line up the beeps (hence the timer shots).

After we got that up and running, we then hooked up a secondary button to send a tweet saying that the user didn’t want to take a selfie, with an accompanying error beep.

The final product
-----------------

Craig got busy turning our loosely wired prototype into a fully functioning, well designed piece of production ready technology. The results can be found in the following terrific tweet:

%[https://twitter.com/craigburgess/status/972492065941737472]

You can see the results of The Selfie Button in action here: https://twitter.com/theselfiebutton

We won!
-------

I left early, but Craig put together a great presentation about how the Selfie Button added a bit of delight to peoples lives:

%[https://twitter.com/julies_cr8tve/status/972497287485288448]

We won! Along with a deserving Matt Brailsford’s Alexia hack.

%[https://twitter.com/craigburgess/status/972523695943159808]

Get Hacking!
------------

All in all it was a fantastic weekend of hardware hacking fun, and has inspired me to get my very own Raspberry Pi on order for a bit of tinkering at home. I would highly recommend attending, or even [running a hackathon](https://wholesomecode.ltd/blog/hackathon-barnsley-2/) in your area!

%[https://twitter.com/ceribatchelder/status/972563320439009285]

%[https://twitter.com/mattwatsoncodes/status/972478033851551750]