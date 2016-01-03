---
category: blog
layout: default
published: true
title: PHP switch script
---
As promised in [PHP 5.2.x and 5.3.x side by side ](../2009/10/30/php-5-2-x-and-5-3-x-side-by-side/)here's a script that switches not just the php module for Apache but also the command line version

```
#!/bin/bash
# This script relies on you having two copies of libphp5 in /usr/lib/apache2/modules/
# - libphp52.so and libphp53.so
# as well as two copies of php in /usr/bin/
# - php52 and php53
# it will try to determine current version by checking the mentioned files and then
# switch to the other
currentlib=`ls -la /usr/lib/apache2/modules/libphp5.so | awk '{print $10}' | awk -F '/' '{print $6}' | sed 's/[a-z\.\/]//g'`
currentbin=`ls -la /usr/bin/php | awk '{print $10}' | sed 's/[a-z\.\/]//g'`
if [ "$currentlib" != "$currentbin" ]
then
    echo "PHP bin and lib are not in sync"
    exit 1
fi
if [ ! -h "/usr/lib/apache2/modules/libphp5.so" -o ! -h "/usr/bin/php" ]
then
    echo "libphp5.so or /usr/bin/php is NOT a symbolic link as it should be"
    exit 1
fi
if ! `rm /usr/lib/apache2/modules/libphp5.so /usr/bin/php`
then
    echo "Could not delete old libphp5.so or php bin link"
 exit 1
fi
case $currentlib in
    "53")
        ln -s /usr/lib/apache2/modules/libphp52.so /usr/lib/apache2/modules/libphp5.so
        /etc/init.d/apache2 restart
        ln -s /usr/bin/php52 /usr/bin/php
        echo "Switched to PHP 5.2";;
    "52")
        ln -s /usr/lib/apache2/modules/libphp53.so /usr/lib/apache2/modules/libphp5.so
        /etc/init.d/apache2 restart
        ln -s /usr/bin/php53 /usr/bin/php
        echo "Switched to PHP 5.3";;
    *)
        echo "Something went wrong, current file doesn't match what it should: $currentlib"
        exit 1;;
esac
exit 0
```

As the note says in the script, you need a setup with multiple version of libphp5x and php5x in the /usr/lib/apache2/module/ and /usr/bin/ folders. The script will do SOME sanity checking before switching but there's obviously still the potential for strange things happening.
