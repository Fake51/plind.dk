---
category: blog
layout: default
published: true
title: Sorting algorithms - quick update
---
In [Sorting algorithms: bubblesort](http://plind.dk/2009/11/06/sorting-algorithms-bubblesort/) I started working on implementations of different sorting algorithms. The first obviously being bubble sort. I hadn't worked up a proper framework for the implementations though, so the implementation was just a flat file - not great for reusing code. Seeing as I plan to do about 10 or more algorithms I thought it might be best to get the framework done before I work on other algorithms. So here's the code for it.

## Base code
The base code includes stuff for setting the test up and outputting the result. It also includes the base sort class which sort implementations will extend.

```
<?php

if (!empty($_SERVER['argv'][1]))
{
    $sortclass = $_SERVER['argv'][1];
    $sortfile = strtolower($sortclass);
    if (!is_file($sortfile . ".php"))
    {
        die ("Could not find {$sortfile}.php, quitting\n");
    }
    require_once "{$sortfile}.php";
}
else
{
    echo "No comparison done, running just native PHP sort()\n";
}

$rand = file_get_contents('rand.txt');
$random_array = explode(',', $rand);
echo count($random_array) . " elements to sort.\n";

$a = new BaseSort($random_array);
$a->runSort();
printf ("Sanity check. PHP native sort() algorithm took: %f seconds. Memory used: %d bytes\n", $a->timeTaken(), $a->memUsed());

if (class_exists($sortclass))
{
    $b = new $sortclass($random_array);
    $b->runSort();
    printf ("Running {$sortclass} sort. Algorithm took: %f seconds. Memory used: %d bytes\n", $b->timeTaken(), $b->memUsed());
    if (!$b->validateResult())
    {
        echo "{$sortclass} sort implementation failed.\n";
    }
}

class BaseSort
{
    protected $store;
    private $_start_time = 0;
    private $_end_time = 0;
    private $_start_memuse = 0;
    private $_end_memuse = 0;

    public function __construct(array $array)
    {
        $this->store = $array;
    }

    /**
     * returns the amount of time used by the sort function
     *
     * @access public
     * @return float
     */
    public function timeTaken()
    {
        return $this->_end_time - $this->_start_time;
    }

    /**
     * returns the amount of memory used by the sort function
     *
     * @access public
     * @return float
     */
    public function memUsed()
    {
        return $this->_end_memuse - $this->_start_memuse;
    }

    /**
     * runs the sort function
     *
     * @access public
     * @return void
     */
    public function runSort()
    {
        $this->_start_memuse = memory_get_usage();
        $this->_start_time = microtime(true);

        $this->sortFunction();

        $this->_end_time = microtime(true);
        $this->_end_memuse = memory_get_usage();
    }

    /**
     * the actual sort function to use
     *
     * @access public
     * @return void
     */
    public function sortFunction()
    {
        sort($this->store);
    }

    /**
     * validates the result
     *
     * @access public
     * @return bool
     */
    public function validateResult()
    {
        for ($i = 0; $i < $count - 1; $i++)
        {
            if ($this->store[$i] > $this->store[$i+1])
            {
                return false;
            }
        }
        return true;
    }
}
```

## Subclasses
The base sort class is then to be extended by sorting algorithm implementations in the following manner.

```
<?php

class BubbleSort extends BaseSort
{
    public function sortFunction()
    {
        $count = count($this->store);
        $array = $this->store;
        for ($i = 0; $i < $count - 1; $i++)
        {
            for ($ii = 0; $ii < $count - 1; $ii++)
            {
                if ($array[$ii] > $array[$ii+1])
                {
                    $temp = $array[$ii+1];
                    $array[$ii+1] = $array[$ii];
                    $array[$ii] = $temp;
                }
            }
        }
        $this->store = $array;
    }
}
```

Sorting an array using this then comes down to instantiating a subclass and doing $sort->runSort(). The implementation could have been using composition rather than subclassing (using the [strategy design pattern](http://en.wikipedia.org/wiki/Strategy_pattern)) but for this exercise there's no reason for that.
