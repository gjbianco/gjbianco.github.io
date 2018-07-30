---
title: Controlling photoanalysisd
date: 2018-07-29 21:04:36
tags:
---

## Background

I recently bought a new MacBook. This article gets better, I promise.

I decided to set it up fresh instead of restoring from a backup. I like to take the time to try and simplify what I've got on my machine.

Naturally, I expect most applications to take a little bit to sync down settings, files, etc. All of Apple's stuff in particular has a lot to do and I'm fine with that. However, the one I am not so fine with is Photos.

Specifically, a process called `photoanalysisd`. 

This is a background service that, as the name implies, is responsible for analyzing your photos. For the most part, it seems like it is trying to recognize people. This service is directly tied to the "People" tab in Photos (more on this in a moment).

The fact that this happens on your machine and *each* of your machines is a good thing! There may be some things that get sent off to Apple, but the majority of the work seems to be happening on your hardware.

What is not so good is the level of control you have around if/when/how this service is controlled. Or, in this case, how it seems to control your machine.

If you go into the People tab of Photos before it is done analyzing, you will see its progress. Even if you have a fairly beefy machine, this process takes a long time. Weeks kind of long time. The only exception seems to be if you have a photo library barely into or lower than quadruple digits.

On the surface, this shouldn't be an issue. The sorts of analysis its doing is probably computationally hard. It uses a good amount of resources, but doesn't try to fully harness the machine since it is meant to be a background process that occurs over the course of days.

The main issue seems to be that you have no control over this process. I never plan to use the People tab, yet my machine still has to painstakingly go through my entire library. I have a medium size photo library (~10k) and after a few hours on the 12" i5 MacBook, it has managed to go through almost 300 of them." Many people complain about their way more powerful iMacs taking weeks to go through libraries of similar size.

During this process, the machine is still usable, but it doesn't go unnoticed. It does use about 25% of the CPUs constantly. This causes the fanless machine to constantly stay pretty warm and chug on some things. Usually not enough to notice, but seems like it might cause someone to be less than impressed with their *brand new* device if they didn't know this was going on.

On top of that, it is almost impossible for a normal user to learn this is going on. The only mention of "photoanalysisd" is in Activity Monitor. This should not be the only location to learn about something that is using days of CPU time. For a feature I imagine a lot of people either don't want or don't care about having on every device with Photos.

Yet it gets worse.

There are seemingly very few ways to actually get it to stop or even pause. Supposedly, it only runs when a) the machine is plugged in and b) Photos isn't running. This means that people that want it to pause so they can get CPU cycles back resort to running off battery unnecessarily or leaving Photos open for no reason. Even leaving Photos *minimized* makes it start up again. However, mine still runs even with Photos focused, so I guess it doesn't really matter.

You can also just let it finish, but this takes quite a long time (well after their 14 day return policy is up). It also seems like it gets stuck for some people, never finishing.

You can use launchctl to disable and kill the service. However, this is only a temporary reprieve (under an hour), as the OS will restart the service automatically for you. How nice.

You might be able to (re)move the service file, but that may or may not affect system stability. Not something I really want to try, personally. YMMV.

After all that, we're finally to my workaround...

## Kill It With Fire. Repeatedly.

The family of operating systems that macOS belongs to usually come with an awesome little tool. This tool, called cron, lets you run commands in the background automatically.

Each of these commands, a cron job, can be configured to run on an interval. For example, you could have it run a command to download a particular file every day at 6am. Maybe you want a cron job to delete your internet history on the first day in June. Sure, it can do that, too. How about make you some coffee every 20 minutes? I feel like cron is not the hard part, but sure, why not.

Maybe you want it to kill a pesky background service literally every chance it gets (hint: this is exactly what we want to do).

We're going to make a cron job that kills the photoanalysisd service every minute. The command takes less than 10ms to run and doesn't error out if the service is already dead.

We do this every minute because a) I don't know exactly how long before the service starts again, b) even if we knew, having them get out of sync would allow photoanalysisd to run longer, and c) cron can only go down to minute granularity (I would've done it every 20 seconds or so).

This does mean that photoanalysisd does potentially get ~59 seconds to run. However, assuming that it gets started exactly X minutes after getting shut off, it probably won't get to run very long.

Okay, here's how to actually do it.

## sudo avada kedavra

First, you'll need to grab your user's UID to pass to launchctl:

`echo $UID`

The launchctl tool requires root privileges (it controls system services). This means that our cron job needs to be for the root user.

Edit the crontab for root:

`EDITOR=nano sudo crontab -e`

Add this line to the file:

`*/1 * * * * launchctl disable gui/<UID>/com.apple.photoanalysisd && launchctl kill -TERM gui/<UID>/com.apple.photoanalysisd`

Replacing <uid> with your user's UID. For example, if your UID was 123, you'd add:

`*/1 * * * * launchctl disable gui/123/com.apple.photoanalysisd && launchctl kill -TERM gui/123/com.apple.photoanalysisd`

Now save the file with ^o (Control + o) and then Enter.

Close the editor with ^ (Control + x).

## Closing Thoughts

First off, if anybody from the Photos team somehow ends up reading this, I'm begging you to give us an off switch for the People tab. I don't use it, I don't plan to use it, and it is a huge waste of resources.

Second, this method is about as crude as it gets. It would be like using a pump and some garden hose to refill your gas back into your leaking tank. However, it does allow me to use my machine in peace.

Maybe someday I'll improve this method. Possibly using launchd, itself.

I hope I never get the chance.
