---
category: blog
layout: default
published: true
title: Sorting algorithms - bubblesort
---
I decided recently that it was time to add to my knowledge of sorting algorithms. I didn't just want to read some quick descriptions on names of algorithms but to actually prod my knowledge-muscle with some stuff. So, I started out on Wikipedia, reading [the article on sorting algorithms](http://en.wikipedia.org/wiki/Sorting_algorithm) while taking notes. It gives a good overview of things and has tons of stuff leading off to other articles and the web. The next step was getting more into details with the various algorithms, to get some idea of how they really work and what makes them efficient or not. Again, I mainly used Wikipedia for this, although for a few of the articles I found it rather helpful to google around.

The last part of the exercise, and the most fun, has now come: implement the algorithms and test them :)
## The setup
I don't want this to be a massively complicated affair - the idea is to get some hands-on with a number of sorting algorithms. Hence, I'm limiting myself to working on an array of (pseudo)random integers in the range of 1 - 1.000. The array will be of varying length to determine efficiency in scaling - however, to get some sort of fair test the array will be the same for the different algorithms (well, it will in the end when I've done all of them).

The first step was then to build a random-array generator. Not exactly rocket-science, but still needs to be done. To be able reuse the array of random numbers, I opted to write it out to a file, so it would be easier to run separate tests on it. Here's the code I ended with:

```
#!/usr/bin/php
<?php

if (empty($_SERVER['argv'][1]) || !($count = intval($_SERVER['argv'][1])))
{
    die("Input value is zero, need a proper number of random numbers to generate.\n");
}

$fh = fopen("rand.txt", 'w');
$first = true;
$write = '';
for ($i = 0; $i < $count; $i++)
{
    if (!$first)
    {
        $write .= ",";
    }
    $write .= mt_rand(0,1000);
    $first = false;
    if ($i % 100)
    {
        fwrite($fh, $write);
       $write = '';
    }
}
if ($write != '')
{
    fwrite($fh, $write);
}
fclose($fh);
```

This will happily chug out random numbers separated with a comma. This can then be read from the file and explode()d - and you've got an array of random integers.
## Bubblesort
This algorithm is one of the most basic sorts there is, taught to beginners so they can grasp the basic ideas of algorithms and sorting. In terms of efficiency it's one of the worst sorting algorithms one can find. Hence, a good starting point :)

Here's the code I came up with. It ain't pretty but it does the job.

```
<?php

$rand = file_get_contents('rand.txt');
$random_array = explode(',', $rand);
$random_array_copy = $random_array;

$start_time = microtime(true);
sort($random_array_copy);
$end_time = microtime(true);
echo "Sanity check: PHP native sort() algorithm took: " . ($end_time - $start_time) . " seconds\n";

$start_time = microtime(true);
$count = count($random_array);
for ($i = 0; $i < $count - 1; $i++)
{
    for ($ii = 0; $ii < $count - 1; $ii++)
    {
        if ($random_array[$ii] > $random_array[$ii+1])
        {
            $temp = $random_array[$ii+1];
            $random_array[$ii+1] = $random_array[$ii];
            $random_array[$ii] = $temp;
        }
    }
}
$end_time = microtime(true);
echo "Bubble sort of ${$count} elements took: " . ($end_time - $start_time) . " seconds\n";

for ($i = 0; $i < $count; $i++)
{
    if ($random_array[$i] != $random_array_copy[$i])
    {
       die("Bubble sort failed");
    }
}
```

Before I do the next algorithm, I need to get the framework code out of this - there's no point to copypaste stuff in your files. It works though, which was the basic requirement for this to be a fun experiment :)

## Results
Testing the implementation gave the following results:

```
Sanity check: PHP native sort() algorithm took: 0.00237798690796 seconds
Bubble sort of 1000 elements took: 0.577649116516 seconds
```

For 1.000 elements it's more than 20 times slower than the PHP sort() function.

```
Sanity check: PHP native sort() algorithm took: 0.0431900024414 seconds
Bubble sort of 10000 elements took: 59.5861978531 seconds
```

Scaling up by a factor of 10 changes the result drastically - the time used is 100 times bigger than for 1.000 elements. In comparison, the PHP sort() function only doubled it's run time.

So yeah, there you have it. Bubblesort running in O(n<sup>2</sup>). Definitely not something to use, though.

Note: the reason behind comparing with PHP's built-in sort() function is mainly to keep the general focus. Don't ever use a custom built sort function in PHP if the built-in will do the trick. The bubblesort used here is 1379 times slower than the PHP sort() function ...
