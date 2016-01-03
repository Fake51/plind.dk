---
category: blog
layout: default
published: true
title: Input validation fail
---
I just couldn't help it, I had to post this little nugget of failure.

On the website of a Danish newspaper - [Politiken](http://www.politiken.dk/) - they do blogs as well as newsbits (and I mean bits, most of the stuff is just Reuters/AP). On their blogs, comments are of course allowed. So far, so good. Now the problems start when Politiken accept the comments. What they **should be doing** is check the various fields for meaningful and/or default input. What they **are doing** is allowing anything that looks remotely meaningful. This leads to beautiful bits of html like the following:

```
[[name removed]](http://Website)

```

Failing to check for the default value your own website put into the field is simply pathetic. More pathetic than not checking whether the referenced site is actually a valid domain but not by much.
