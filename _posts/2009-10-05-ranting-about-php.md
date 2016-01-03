---
category: blog
layout: default
published: true
title: Ranting about PHP
---
Came across [this blog post on PHP](http://www.prodevtips.com/2009/09/30/this-is-whats-wrong-with-php/) today. Seems to me the author needs a fair amount of sleep and then a cop of coffee or two ...

First,
> ... it all started with me trying to be clever with **array_map** and **array_filter**.
Being clever is not a good thing. In my experience it always leads to crappy code that you'll regret ever having done when time comes round to code maintenance. Whenever the thought strikes you - "I'll come up with a clever solution for this" - think twice, then step back and redesign.

Secondly,
> I knew array_filter existed and what it was all about since before, however I started working with something requiring array_map first, all well and OK, array_map looks like this: array_map(’callback’, Array). So then I assumed I could use array_filter in the same fashion, big mistake.
As Henrik goes on to point out, PHP is not consistent in function definitions, which is a problem. A much bigger problem is that something with 5 years of self-proclaimed PHP-hacking experience doesn't check function definitions but wastes time assuming either that 1) function definitions are the same for similar functions (which apparently he knows is not the case) or that 2) he knows the functions already when he doesn't. How many tools are there for checking PHP function definitions easily? A simple search key in FireFox would have solved this in 30 secs.

Thirdly,
> So the class method containing both the array_map and array_filter callbacks has only one argument: $name, which contains a partial string to match against users’ full names or usernames. I wanted to use that variable inside the array_filter callback.

What was I thinking? I **knew** that it wouldn’t work, of course it wouldn’t since callbacks are just an ugly hack bolted on top of PHP, hell you’re calling the name of the callback in the form of a string. I’ve got no idea how the interpreter is actually evaluating this stuff and I don’t want to know, I’d rather keep what I have in my stomach where it is.
Yes, callbacks are nasty but they provide for quite a lot of functionality. You can use the name of a function, an object method, the result of create_function or even (from PHP 5.3) a PHP closure. But of course, that doesn't directly solve Henriks problem: passing that extra variable to array_filter or array_map. And here there obviously is a problem that could use a nice and simple design like allowing for passing an extra variable. Workarounds are possible, though:

```
<?php
class dummy
{
    private $_partial;
    public function getFriendsByPartial($partial)
    {
        $this->_partial = $partial;
        return array_filter($input_array, array($this, 'callback'));
    }

    public function callback($input)
    {
        // test $input against $this->_partial here
    }
}
```

Is that pretty? No. Does it work? Pretty sure it does. Does it keep things inside the class? Yup. Did it take me longer to write than it took me to read Henriks post? No.

However, this was apparently not clean enough, so instead of doing something simple like this, the alternative was writing up a function using foreach and stripos. Only Henrik couldn't remember (after 5 years) that there's strpos and stripos (and, again, checking function lists was not an option) so he wasted more time.
> I probably lost over an hour just on that one and it’s happened to me at least 5 times, yes roughly one time per year, just this fucking shitty little function. Why oh why are there two of them? You have to be rainman to navigate this swamp, of course I should’ve used **stripos()**! What an idiot I am right? Why couldn’t my pea brain remember that, especially since it’s happened to me so many times before? I must truly be a complete moron.
I don't think he's a moron for not remembering the difference between strpos and stripos (or rather that both exist). If there's anything at play here, it's the belief that you can actually remember all the functions of PHP. So when the ironic morale comes
> When it comes to PHP, don’t try to be clever, and most importantly of all, know your functions, all 5000 of them.
it's very hard not to just shrug it off and think that the programmers arrogance got to Henrik. Don't ever think you can know all of the functions in PHP - look them up before you start abusing, that way you'll avoid spending more than 5 minutes on the thing that cost Henrik hours.

I'm glad that in the end though, he did gain the insight of not trying to be clever.
