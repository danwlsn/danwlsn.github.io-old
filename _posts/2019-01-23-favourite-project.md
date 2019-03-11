---
layout: post
title:  "My golden child"
date:   2019-01-23 10:00:00 +0100
author: Dan Wilson
---

I built a coffee cam for my previous job at BJL. It was a Raspberry pi with a camera attached to it. A bash script would take a picture every thirty seconds and post the pictures to an API. The backend was run from Django. And this is where it gets interesting.

The Django app would receive the picture and crop down just the pot of coffee[1]. Then it would measure how much coffee was in the pot[2]. Again, once we could measure the pot, we could start to use the data.

Slack is great for alerts, they have a super simple API and a library for most languages. The simple notifications we came up with “There’s a full pot of coffee” and “The coffee pot needs refilling”. Easy, we know the level of the coffee so we can easily figure out if it needs filling or is nearly full[3]. Great! We have a our IoT things coffee cam from a cheap coffee pot, a raspberry pi with a camera and some technology gaffe tape.



****



[1] You’d think I did this with a image recognition library, but that would be the easy option. No I lined the camera and marked where the coffee machine needed to be. And then got the exact cropping measurements I needed from manually cropping the image and saving the coordinates. Then hard coded those values into the crop function. So every time someone moved the coffee machine, everything fucking broke. 

[2] Again, you’d think I’d use some line detection here from the bottom of the pot to the level of the coffee. But no, that was the easy option. Instead I turned the whole picture to black and white. Literally, 1s and 0s, pure (255, 255, 255) and (0,0,0). And the amount of coffee in the pot would be proportionate to the amount of black pixels. 

[3] As you can imagine, we did it the hard way. We know the amount of black pixels is the amount of coffee. So I got a picture of a 1/3 pot of coffee and another of a full pot of coffee. Ran the same code over the pictures that gives me the amount of black pixels and hard coded those bad boys in the notification service. Oh yeah, not even constants. You would have thought I’d learnt from the first time.

I also have other stories about how this was such a terrible system. <br >Ask me on Twitter - [Dan Wilson (@danwlsn) on Twitter](https://twitter.com/danwlsn/).

I think the moral of the story here is that it doesn’t matter if you miss the mark on your solutions, you’re still **getting shit done**.

Here are some other reasons I loved the project so much:

1. It was useful. It ran for a while, and I used to love seeing the coffee is full notification and getting the fresh coffee someone else made. I don’t think I made coffee for well over a year. 
2. It was totally different to what I was working on day to day. I built a lot of similar projects there and this project was so different from them. If anything, it was my first distributed application.
3. Following on from number 2, I got paid to do this. I fit this in around my day to day stuff. It made me work really hard on “their” work so I could focus on the fun stuff. I’m not sure if they know just how many hours/days I put into this.
4. It was also the first time I got to work with [Joe Sparrow](https://twitter.com/ANewBandADay). He was as jazzed as I was about this. And while, he didn’t write any code or anything I don’t think I could have done it with out him. It was as much his project as it was mine. I think it might have been his idea.. but I wrote the code.
