---
category: blog
layout: default
published: true
title: PHP 5.2.x and 5.3.x side by side
---
A while ago I ripped out my PHP 5.2 installation (nicely handled by apt-get) in favour of manually installing 5.3 so I could play around. Yesterday I then installed Ubuntu 9.10 ([info and download at ubuntu.com](http://www.ubuntu.com/)) and after quite a lot of installing I suddenly found myself without a working development environment. The cause was an upgrade to mysql that upped the library number from 15 to 16 - and seeing as I had manually configured and compiled php it was compiled against version 15 of the library.

After some cursing and swearing I then removed my 5.3 install of php and let aptitude do it's work installing tons of php stuff for 5.2. And once again, I had a working dev environment ... but without the niceness of 5.3. Seeing as all work and no play makes Jack a dull boy, something had to be done, and I settled on installing 5.2 and 5.3 on the same box and switching between them as needed. That should let me play with 5.3 while always having a working backup in 5.2 when things get upgraded/updated. The prospect of installing php 5.3 wasn't nice though, as it had taken me some time first round and obviously resulted in trashing my install (with aptitude swearing I didn't need about 100 packages here and there). This time it had to work better.

First, I googled installing php 5.3. [Brandon Savage's post on the topic](http://www.brandonsavage.net/installing-php-5-3-on-ubuntu/) came up and I figured it might do the job, which, with some minor changes, was indeed the case. The main thing to do different than Brandon is follow the advice about using the --prefix option. After that, you just need to add some shell scripting to get you going.

In details, here's the plan:

1. Install PHP in the latest version from apt-get (5.2.10 when writing this) and apache/mysql if you need them.
2. Install the other dependencies listed on Brandons blog, if you don't have them already
3. Check to make sure you've got a working PHP 5.2 install, if not, you need to fix that before going further.
4. Make a backup of libphp5.so (this is the 5.2 version, which you'll need for switching)
  1. Easiest is just copying it with sudo cp /usr/lib/apache2/modules/libphp5.so /usr/lib/apache2/modules/libphp52.so.orig
5. Follow Brandons blog on installing 5.3 up to the point of configuring it. Then make sure you add an alternative directory for your 5.3 installation (say, /opt/php5.3 or so).
6. Go through with the installation till you're done with make -i install. Then head into /usr/lib/apache2/modules/ and backup libphp5.so again (it's now version 5.3).
  1. This time, following the scheme from above, copy it to libphp53.so.orig.
7. Copy the backup files to libphp52.so and libphp53.so respectively. Don't move them, copy them (otherwise there's a fair chance they'll be overwritten on subsequent PHP updates).
8. Remove libphp5.so from /usr/lib/apache2/modules/ and make a symbolic link in that directory to either libphp52.so or libphp53.so depending on what you want.
9. You're almost done, just need a way of switching back and forth. To achieve that, you can use the script below.

Copy the following script and paste it into a file. Then chmod the file to 0755 so you can execute it. Make sure you either stick file in your include path or make note of where it goes.

```
#!/bin/bash
current=`ls -la /usr/lib/apache2/modules/libphp5.so | awk '{print $10}'`
if [ ! -h "/usr/lib/apache2/modules/libphp5.so" ]
then
 echo "libphp5.so is NOT a symbolic link as it should be"
 exit 1
fi
rm /usr/lib/apache2/modules/libphp5.so
if [ $? -ne 0 ]
then
 echo "Could not delete old link"
 exit 1
fi
cd /usr/lib/apache2/modules
case $current in
 "libphp53.so")
 ln -s libphp52.so libphp5.so
 /etc/init.d/apache2 restart
 echo "Switched to PHP 5.2";;
 "libphp52.so")
 ln -s libphp53.so libphp5.so
 /etc/init.d/apache2 restart
 echo "Switched to PHP 5.3";;
 *)
 echo "Something went wrong, current file doesn't match what it should: $current"
 exit 1;;
esac
exit 0
```

This file will switch between PHP versions for your apache install and will restart the apache server so the change is instant. You need to run the file with sudo, as otherwise you won't be able to remove the symbolic link or create the new one.

In much the same way, you'll be able to switch the CLI version of PHP. I'll post an updated version of the switch script in a followup post at some point.

One last thing: check that things are working by loading a phpinfo() output page in your server. You should see the difference in the version reported (5.2.10 vs. 5.3).

**Edit:** There's a better version of the script in [PHP switch script](http://plind.dk/2009/10/31/php-switch-script/) - it switches both CLI and Apache module version.
