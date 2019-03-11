---
layout: post
title:  "How long is a spike"
date:   2018-12-17 06:53:13 +0100
author: Dan Wilson
---
## How I learnt about spikes?

I recently started a new job as a software engineer at a large organisation in
Manchester. My team and I build a maturing peice of software that helps people
manage funerals. This is by far the largest development team I've worked on and
I've learnt so much already. One of the first thing I learnt was the concept of
a spike. I come from doing a lot of agency work, so I had no idea what this was.


## What is a spike?

A spike is where you take a problem you're trying to solve, usually a difficult
one with many possible paths. You assess how you could implement this, and pick
one to "spike" out. When you complete the work, you're then supposed to step
back, assess how the ticket went, and see if there's a better way of doing
it. Then the real important part: *throw your code away, and start again*.


## So how long can a spike be?

Recently I've been picking up some work on a project I've been working on for
the last two years now. It's on it's second codebase, and has two success
spikes.


    1. The first MVP to test the idea was burned down
    2. A small spike was done when redesigning the main screen


The first wasn't really a spike. I started working on the MVP January 2017 and
finished by March 2017. I then started working on the project again in October
2017 and rebuilt from the ground up. Same idea, same framework, but just took
what I leanrt from the first time. I'm currently thinking about doing the same
again.


## When is it okay to spike?

Now I'm going to talk about a couple of senarios I think are acceptable times to
call it a spike:


### Cost of tech debt is more than cost to call it a spike?

Tech debt is normal. No one has no tech debt and if you say you do, you're
lying. At work, we have a lot of tech debt. It does keep piling up and it's
almost like it will never go away. However, we also have a large maturing
codebase. To burn this down and start again and fixing all the tech debt as we
go would be very costly. It's cheaper for us to just try and keep on top of
this.  If however you have a smaller codebase with a lot of tech debt, you might
find it's quicker to get to a clean codebase with no tech debt by burning down
and starting again.


### Getting from A B is quicker if you call it a spike?

If you want to build in a new feature but you're struggling to work through
because of some tech debt or archeticutral reasons, burning down and rebuilding
with the feature could be quicker than just trying to implement the
feature. This usually comes from archeticutral desicions you made earlier on
that are now holding you back.


### When you're struggling with the stucture and you want to call it a spike?

This is the bucket that I'm really in. The strucutre of my codebase is no
_terrible_ but it's bothering me. I can see this becoming a huge problem down
the line and know it will be much more expensive to fix then than now.


## When is not good to spike your whole code base?

### When you're in a large team

Starting a project from scratch with a large team isn't going to be ideal. When
you're starting out on a project you might fine that too many cooks spoil the
broth. Depending on how you were going to tackle the rebuild, if you have a
large team this is going to be a bigger decision.


### When you're working on a monolith and you're plan is to rebuild a monolith

This one is really "Don't rebuild exaclty the same thing". From a spike you need
to take what you've learnt, and do it better. So if you're struggling with your
monolith, you decide to tear it down, and rebuild the monolith but with slightly
better archtecture, you might still find yourself struggling with your monolith.


## Final thoughts

Spikes are good. And not all spikes start out as spikes. It's important to not
get precious over our code. If you're starting to think you wish you could burn
it down and start again, take a step back and think about it as a real
option. Think about the problem you're trying to solve by burning it down. Can
you see real strucuturly problems? Can you see a better way of doing things? If
so, plan your rebuild.
