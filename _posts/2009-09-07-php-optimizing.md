---
category: blog
layout: default
published: true
title: PHP optimizing
---
I've read [various](http://progtuts.info/55/php-optimization-tips/) [tips](http://code.google.com/speed/articles/optimizing-php.html) [about](http://stackoverflow.com/questions/127765/php-optimization-tips) [optimizing](http://www.alexatnet.com/node/196) PHP code and at first I happily took them in. Later on, having read other [points](http://php100.wordpress.com/2009/06/26/php-performance-google/) of [view](http://www.codinghorror.com/blog/archives/001218.html), I started to wonder a bit about some of the optimizations and later still I realised that some things are not just dubious but plain wrong. Yes, I'm talking about strings here.

If you've looked at optimizing PHP code and browsed the net regarding the topic, you will invariably have come across tips amounting to use single quotes wherever possible instead of double quotes. You will almost certainly also have come across articles or blog posts telling you that this is bogus. Initially, looking at the advice, you might think it makes sense: PHP evaluates double-quoted strings to see if they contain variables that need to be inserted into the string. However, if you roll your own tests of this advice, you'll soon see two things: 1) it is not clear in any way that using single quoted strings is faster than double quoted strings and 2) if there is any gain in speed, you won't notice it until you're running millions of iterations. Literally, millions of string operations.

With this in mind, I don't care at all about single or double quoted strings, I just use whatever ... which turns out to be mainly double-quoted strings, because of inserted variables. You see, the thing that I do care about is code legibility. Take the following two pieces of code - which is more legible?

```
$string = "some text and " . $variable;
$string = "some text and {$variable}";
```

To me there's no doubt: the latter is by far more readable. I don't have to jump in and out of strings, I know that it's all one string and I can read it as such. I know that a lot of others have a different coding style though, preferring the former. This got me thinking: is the case the same for the two strings above as for strings without variables? In other words: is there a difference in performance when variables enter the picture?

To answer my question, I wrote a quick test:

```
$count = 20000000;
$start = microtime(true);
$string = '';
for ($i = 0; $i < $count; $i++)
{
    $string = "test string" . $i . "test string" . $i . "test string" . $i . "some string";
}
$middle = microtime(true);
$string = '';
for ($i = 0; $i < $count; $i++)
{
    $string = "test string{$i}test string{$i}test string{$i}some string";
}
$end = microtime(true);
echo "First 20000000 iterations took: " . ($middle - $start) . PHP_EOL;
echo "Last 20000000 iterations took: " . ($end - $middle) . PHP_EOL;
```

I ran it first with a million iterations and just one variable and got about a second for each loop. Seeing as this is rather low, I upped the count to 20 million, which gave a more interesting result - there was about a second difference between the two loops. Trying a couple more times, the difference stayed, so I wondered if there was something significant there. I figured that if anything, then more variables should make it more obvious - which it did: the first test took about 20 secs for both loops, the second set of iterations (two variables) took 33 and 34 secs. Adding another variable took me to 46 and 48 seconds for each 20 million loops - seemed that finally I might be seeing some actual difference. Until I switched the places of the loops, when suddenly the loop that was faster before was the slowest.

At this point, there are two options: look at the memory usage of the loops to see how that plays into things (maybe the difference seen previously can be tracked to that) or call it quits. Looking at the results, calling it quits is the only reasonable thing to. 20 million iterations and no clear result to show for it ... other than that when variables are in the picture, strings take a lot longer to process. Which doesn't leave you with a lot, as you'd have to reduce the amount of variables in the strings to improve speed.

And again: 1 million iterations with 1 variable amounts to 1 (one) second. 3 variables puts it at 2.1 seconds. Don't ever bother with string optimizations. Not worth it unless you're doing many, MANY millions of iterations.
