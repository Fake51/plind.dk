---
category: blog
layout: default
published: true
title: Migrating servers
---
This week and the weekend before that I've had the chance to experience the joy of migrating servers. [BeWelcome](http://www.bewelcome.org) has opted for a new production server, as a) the new contract is cheaper and b) the new server has better hardware. Based on that experience, there are a number of conclusions to draw:

* **Before you do anything that looks like migrating, make sure that everything on the server is documented.**
* **Detail everything that needs to be done before starting.**
* **After that, make sure that everything is backed up.**
* **Start moving things to the new server in good time. Last minute means errors.**
* **Make sure you go through all the phases: preparation, migration, cleanup.**

Getting ready for the server migration, I tried to get down details of everything that needed to be moved. This proved VERY hard, because we had no documentation on what was running on the server. Sure, I know that we're running a website using an Apache webserver and utilizing PHP and MySQL. However, what do we use to send out emails? How are we handling normal backups? Which users are needed, which are outdated and should be removed? Most of these things weren't documented anywhere, so there was a whole lot of detective work to do. And while this can be interesting, you don't want to lose data because of something as simple as this.

There are obviously also further consequences to the lack of documentation: which scripts should be running automatically? Those that run now or less? Should they be running with the privileges they have now or should they actually be restrained? While you don't necessarily want to deal with this during a server migration, this is actually the time when you're most likely to look closer at what's running and how - unless you're doing an actual integrity check of the server (and how often are you doing those? As often as you should or less?).

Another of the points that didn't exactly to plan for our move was backing things up. I didn't give it too much thought, as the data would be present on the old server during the migration and afterwards too. However, we were handing the old server back to the provider, so the data would obviously not be available for long - in fact, the data was available for one day only after the migration was done. We didn't manage to lose anything critical, but there were a few things we didn't manage to get set up properly before the old settings were gone. This relates to the point on documentation: there were some things that were not documented properly and so we didn't know they were needed on the new server. Hence, when we found out, it was too late to go back and check.

Now, one way of handling backup would of course be to just dd the entire disk and be done with it. Which is a good idea if you have the space for it and can transfer it off your server in good time. Even easier, if you're just migrating servers and will be mirroring the old setup, then you can just copy the old server and stick it on the new. In our case, though, we were moving from Debian 4 to 5. Doing a copy of the system wasn't an option, and to make sure that apps were working properly, we needed to get them set up first. Exactly what do you backup then? On a Linux setup, probably /etc, /home, some of /var ... and again, you see the problem of lacking documentation. Guesswork isn't really good enough here.

Because we weren't done with the migration till a day before handing the old server back, there wasn't a lot of time for cleaning up either. This wasn't too big a problem with regards to the old server: files were deleted, the system was reinstalled a couple of times, nothing should remain unless you're either reading the sectors manually or going for the magnetic trace of things - neither of which are too likely. With regard to the new server, there is more cleaning up to do: because of the lacking documentation, some things were setup in less than perfect ways and some data was copied over without ending in the right places. In other words, there's quite a bit of cleanup to do. And because the migration took place too late, there's no original setup to look at for comparison.

For next time
-------------
One thing is for sure - I've definitely learned my lesson from this experience. I'm currently busy writing up documentation on what we're running, where everything is, how to go about setting it up, etc. It should result in two things:

1. Proper documentation of what we use and what we have
2. Proper documentation on how to move it

Together, the two should allow for a fairly easy time when handling the next migration. Not sure I want to be part of that, but at the very least I should make sure anyone taking up the job after me has an easier time. Chances are, though, that I'll be needing the documentation myself before another migration given the daily maintenance tasks ...
