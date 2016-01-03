---
category: blog
layout: default
published: true
title: Sorting algorithms - Shell sort
---
Third in the line of posts on sorting algorithms (first was [Bubble Sort](http://plind.dk/2009/11/06/sorting-algorithms-bubblesort/) and the second was [Insertion Sort](http://plind.dk/2009/11/16/sorting-algorithms-insertion-sort/)) is the [Shell Sort](http://en.wikipedia.org/wiki/Shell_sort). This is basically a variant of the insertion sort, with the difference that you're sorting the array multiple times. Another way of looking at this is that the shell sort is really a second level sort, wrapped around the actual sorting algorithm. The inner algorithm here is the insertion sort but it could technically be anything, owing to what the shell sort actually does: running a number of smaller sorts, gradually sorting the array, before finally going over the whole array.

What makes the shell sort work is that you first divide the array into n smaller arrays and sort them (technically, you don't divide the array into smaller pieces, you just operate on sub-parts of it). After this, you divide this, now half-sorted, array into n/2 arrays, sort each of these in turn. Iterate till you're working on the whole array. The trick is that each time you sort one of the sub-parts, this also sorts the array. Because using insertion sort on an already sorted array approaches O(n), the final sort of the entire array in shell sort is much faster than the equivalent insertion sort on an unsorted array.

## The Code

```
<?php

class ShellSort extends BaseSort
{
    public function sortFunction()
    {
        $count = count($this->store);

        $array = $this->store;
        $gap = floor($count / 3);
        do
        {
            for ($i = 0; $i < $gap; $i++)
            {
                $temp = array($array[$i]);
                for ($ii = ($i + $gap), $a = 1; $ii < $count; $ii += $gap)
                {
                    $value = $array[$ii];
                    $b = $a;
                    while ($b > 0 &amp;&amp; $temp[$b - 1] > $value)
                    {
                        $temp[$b] = $temp[$b-1];
                        $b--;
                    }
                    $temp[$b] = $value;
                    $a++;
                }
                for ($ii = $i, $a = 0; $ii < $count; $ii += $gap)
                {
                    $array[$ii] = $temp[$a];
                    $a++;
                }
            }
        }
        while ($gap = floor($gap / 2));
        $this->store = $array;
    }
}
```

The difference to the insertion sort should be readily seen: the outer loop dividing the main array into smaller arrays wraps around an insertion sort.

A note on the algorithm: the sub-parts are taken from the original array by picking every n element. This means that when you move an element in the array, you move it n steps at a time. Compare this to the bubble sort, where an element is only moved one step at a time, and you can probably see why this would be a lot faster - even if the inner loop is the bubble sort.

Some notes on the code: it could obviously do with some proper variable naming - it's the result of a couple of iterations trying to improve the efficiency. At first I had opted to run the inner insertion sort inline on the array but after getting some fairly bad results I opted for using a temporary array before reinserting the elements into the array to sort.

## Results
First, the 1.000 elements:

```
1000 elements to sort.
Sanity check. PHP native sort() algorithm took: 0.003565 seconds. Memory used: 64464 bytes
Running ShellSort sort. Algorithm took: 0.044342 seconds. Memory used: 64376 bytes
```

At 1.000 elements, the shell sort is about nine times faster than insertion sort. The next test is 10.000 elements.

```
10000 elements to sort.
Sanity check. PHP native sort() algorithm took: 0.044411 seconds. Memory used: 665912 bytes
Running ShellSort sort. Algorithm took: 0.633066 seconds. Memory used: 665816 bytes
```

Still running slower than the built in PHP sort but nothing remotely close to what could be seen with insertion sort. At 10.000 elements, shell sort is running 50 times faster than insertion sort and only 15 times slower than the run at 1.000 elements. The insertion and bubble sorts were so slow that there was no point trying to run them for 100.000 elements but here it makes sense.

```
100000 elements to sort.
Sanity check. PHP native sort() algorithm took: 0.600952 seconds. Memory used: 6524656 bytes
Running ShellSort sort. Algorithm took: 8.780088 seconds. Memory used: 6524588 bytes
```

Again, adding 10 times the elements ups the execution time by 15 times - nothing like the O(n2) of the insertion sort or bubble sort.

With shell sort, we've reached something that would actually work in a normal environment. There's of course no reason whatsoever to use it if the PHP sort function does the job - but if it doesn't here's a plausible algorithm to use. Not, as I expect to show later, as efficient as other algorithms, but definitely worth a look.
