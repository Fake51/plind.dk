---
category: blog
layout: default
published: true
title: Screen blues
---
Because I'm freelancing for my previous employer, telecommuting in effect, I've become a big fan of screen. I didn't see the benefit of using the program at first, but when you're logging into a machine with ssh and then have to log into one or two more, after that, you soon become very happy about the ability to just open another terminal window on the machine - no need to ssh again (to be fair, you could of course use a master connection for your ssh connections and then just hop through logins with multiple ssh commandlines in one go). Even better, screen lets you detach from a session, with that session still running. You can then drop your connection, come back half an hour later, re-attach to your screen session and find everything as you left it. No wonder screen is such a loved tool by so many people.

One thing has been bugging me though: one of my favourite keymappings for vim stopped working after I started using screen. Specifically, I could no longer get &lt;S-TAB&gt; to work: shift-tab was gone! I couldn't see any way to get it to work and I just left it like that - annoyed that it didn't work but not annoyed enough to really do something about it. For some strange reason, this is one of those issues that seems to have affected only me and one other user (could not find any more pages on Google, just one guy lamenting that the key-combo stopped working). The alternative is of course that everyone that ever came across this instantly knew how to fix it. Something I'm obviously not ready to accept ... though I might have to.

In the end I did manage to find my fix and like so often before, I'm almost ashamed of not having thought of it before. The problem was down to screen not knowing which terminal to emulate and so the fix consists in telling screen to use xterm.

```
term xterm-256color
```

If you put the above line (assuming you're using the 256 color version of xterm and not just xterm) into your .screenrc file, screen happily accepts shift-tab as a key-press combination (it's actually more likely that the problem lies with how Vim interprets the input from screen. I tested the output of shift-tab and it was identical for using screen and not using screen. Vim however had no idea what was going on). If specifying the terminal in your .screenrc is not an option, you can specify it on the command line:

```
screen -T xterm-256color
```

Anyway, I needed to tell the world about this on the off-chance that someone else finds themselves in the same position. I would have loved to find the answer after two minutes on google rather than after 1 hour (there's another gripe: googling for tips/hints/help on screen is nigh impossible using google, as you won't get any useful results, just stuff about monitors or tv screens).
