---
category: blog
layout: default
published: true
title: Sorting algorithms - Insertion Sort
---
Second up in the line of sorting algorithms I'll run through is the [insertion sort algorithm](http://en.wikipedia.org/wiki/Insertion_sort). It's fairly different from the bubblesort algorithm in that bubblesort will go through every element that you're trying to sort and compare it with every other element (meaning, you're looking at about n * n comparisons) while insertion sort will try to minimize the comparison by ordering the elements better. Essentially, insertion sort takes one element at a time and inserts it into another array in it's right place. For each element, you only compare it with other elements until you find one that's smaller than the element you're trying to insert.

In terms of steps, this means something like:

1. Set a to value of element to insert.
2. Set x to index of last element in sorted array.
3. Compare a to sorted_array[x].
4. If a is bigger than sorted_array[x], insert a at sorted_array[x+1]. Done.
5. If a is smaller than sorted_array[x], shift value at x one place up in the sorted array and decrement x.
6. Repeat from 3 till done.

## The code
It's probably easier to figure out what's going on by looking at the code - it's really rather simple.

```
<?php
class InsertionSort extends BaseSort
{
    public function sortFunction()
    {
        $count = count($this->store);
        $result = array();
        $result[0] = $this->store[0];
        for ($i = 1; $i < $count; $i++)
        {
            $value = $this->store[$i];
            $j = $i - 1;
            while ($j >= 0)
            {
                if ($value < $result[$j])
                {
                    $result[$j+1] = $result[$j];
                    $j--;
                }
                else
                {
                    break;
                }
            }
            $result[$j+1] = $value;
        }
        $this->store = $result;
    }
}
```

Note: The code here makes use of the small test framework I created it [Sorting algorithms: quick update](http://plind.dk/2009/11/07/sorting-algorithms-quick-update/).

Quick run through: first, some housekeeping. An array for the result is created, a count of the elements to sort is done and the first element in the sorted array is inserted (it will always be the same element, so better initialize it like this). Then the actual sorting takes place, looping over every element and inserting it into the results array. This is the more interesting part: the element to sort is compared to the top of the results array and if it's smaller, the results array then gets shifted. This keeps on going till a proper place is found for the element to sort.

## Results
First, a test with 1.000 elements.

```
1000 elements to sort.
Sanity check. PHP native sort() algorithm took: 0.003070 seconds. Memory used: 64464 bytes
Running InsertionSort sort. Algorithm took: 0.326779 seconds. Memory used: 64344 bytes
```

At 1.000 elements, it's running in 3/5 the time of the bubblesort algorithm.

```
10000 elements to sort.
Sanity check. PHP native sort() algorithm took: 0.039953 seconds. Memory used: 665928 bytes
Running InsertionSort sort. Algorithm took: 32.702462 seconds. Memory used: 665788 bytes
```

Scaling up by a factor of 10 results in 100 times longer execution time. This is as expected: insertion sort should run in O(n<sup>2</sup>). The reason for this is that while insertion sort minimizes the amount of comparisons to be done compared with bubblesort, it has exactly the same problem: for every element you add that needs to be sorted you're also adding a large number of comparisons to do, even if only half as many as for bubblesort.
