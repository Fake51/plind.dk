---
category: blog
layout: default
published: true
title: Refactoring Infosys
---
The last week or more has seen quite a bit of activity for [Infosys](http://github.com/Fake51/Infosys/) - I've been frenzied, to put it mildly :) Almost every core class has had major changes and the framework is fundamentally different now. It's been lots of fun, lots of learning involved - here are some thoughts on the process:

### 1. Get some overview, create a gameplan
As with all other areas of development (and most things work, really), step 1 should be getting an overview of the current state of things and what needs to be done. If the project is your own (and Infosys, at this point, is pretty much 100% me) there's a fair chance you'll want to just 'dig in' - start refactoring to improve the framework straight away, because 'you know how it works already and how to improve it'. This is a bad idea, because it will inevitably lead straight back to where you are now: refactoring things you're not quite happy with.

In the case of Infosys, lots of core classes were [singletons](http://en.wikipedia.org/wiki/Singleton_pattern) - something I don't have a problem with as such (see [my post on the matter](plind.dk/2009/07/20/lists-of-x)) but was thinking of moving away from in favour of [dependency](http://en.wikipedia.org/wiki/Dependency_injection) [injection](http://www.martinfowler.com/articles/injection.html). My main reasons for not using Singletons anymore were related to testing (using [PHPUnit](http://www.phpunit.de/), to be specific) where singletons can be a pain (although they don't have to be) and to closing classes off as much as possible, decoupling them from one another.

I started out by looking at the core code and looked at what would function better as pure objects and decided on a limited set of classes - moving some functionality out of the request handler into a request object, changing session and log classes. However, I started refactoring things sooner than I should have, believing that I had found a new and better design. The problem was that I didn't actually look at the entire framework core with an objective eye - specifically, I opted to keep the database class a singleton (this app will, for any foreseeable future, ONLY NEED ONE CONNECTION! SO DONT EVEN BOTHER!). I thought it problematic to pass a single database object around (you want to create it when initializing your app if it's database driven, instead of finding out very late in the process that you don't have a database connection) because it meant handing it to other objects that had no reason to have a copy (a request handler shouldn't touch the database) in order to pass it to the ones that need it. As you can probably guess, though, I got over this problem, because the alternative is a) using a registry (which I had been doing but also refactored, again to improve decoupling and testability) or b) direct access to the singleton (which I did for a short period until I decided that really wasn't a very good idea).

The moral here is that I ended up doing more work than I should have done. I should have realised that it would suit Infosys better to not use singletons at all and gone for a more unified refactoring of the core classes. Instead I first refactored code to access the database through the classic getInstance method of the database class, and then afterwards refactored code to just pass an instance of the database class around. The end product is the same, I just spent more time doing it.

### 2. Decide on the overall design
One thing that I've learned from handling several different frameworks and frameworks in different versions is that consistency rules. Creating new features or fixing bugs in old ones is so much easier if the code and surrounding framework is in harmony and if the framework has a driving idea behind it.

One problem with creating a framework over time is that as you learn, you'll be adding new stuff to it. Perhaps some new ways of doing things, new code that works better for some needed things. Unless you really strive to keep everything consistent, you'll end up with having different ways of doing related things in the framework - and this makes creating new features a hassle. This was one of my concerns when refactoring the core classes of the Infosys framework: if half the core classes were refactored to be normal classes and the other half stayed singletons, I would constantly have to guess at which was which. Should I create a new instance of Log? Or should I ask the Registry for the instance? As noted before, I refactored the singletons, but still I have a minor problem here:

```
class Someclass
{
    public function test()
    {
        $log = new Log($this->db);
    }
}
```

The issue in the code above is that the Log class handles both writing to file and writing to the database (the first is used for errors and debug stuff, while the latter is used for logging normal application actions) so the Log class needs a database connection. However, because I'm only dealing with one connection that I want to reuse, the database object is always passed around, while log objects are just created. As both classes constitute core framework code, it would be easier if they worked the same way: less to remember. I could obviously just have opted for creating one instance of the Log class but there wouldn't be much point in passing it around: it does very little and does not need to persist (quite the contrary, in fact).

### 3. Simplicity over cleverness
Part of refactoring is to me the idea of bringing everything together in a consistent idea. You're removing the clutter, bringing things in line with how they should be. Implicit in this idea is also that you should go for the simple design - that clever idea you got a some point is probably the reason you're refactoring things now. I would even take this one step further: if you ever have the choice between clever and simple, avoid shooting yourself in the foot by choosing clever. There's a 95% chance you'll regret going with clever later.

An example of this from Infosys would be the reuse of a searchform. Prior to refactoring, MVC apps used view classes to store all output in - not in itself a clever solution, but unfortunately not a very good one either as some view classes reached the 2k+ lines. Also, controllers called view functions and then returned to the request handler - instead of just telling the request handler which template should be used and then be done with it. The clever part came in reusing template bits, specifically a fairly big searchform containing lots of inputs. The reuse was handled by having a private method in a view class - so the template would just call the private method.

Now, when refactoring to use templates loaded into a generic page class rather than having a view class for each MVC, this becomes a problem: how to get the shared template in a nice and easy way? Templates could always require it, but it would really be nasty to see a require fail in the middle of rendering a template. It would also be possible to hand over the searchform to a helper class, which might in the end be the best solution - however, then the searchform will be removed from it's normal place, which is in the templates folder for the given app.

Now, I may very well go with the latter idea (haven't quite decided yet) as I'm already using a view helper and keeping other bits of template for reuse in there. The problem is obviously that I didn't go with this path for all the reused templates from the start: had I done that, I wouldn't be facing this problem now but would just be able to move on to the next bit of code that needs work. Again, a decision to go for a more clever solution instead of simplicity and consistency has ended up biting me in the rear.

### 4. Test, test, test
One of the key things I wanted to implement in this drive to improve Infosys is unit tests. Again, seeing as Infosys is the product of pretty much just me alone, I have no problem diving into the code and changing some fundamental thing to make it better. This is a good and a bad thing - but luckily the bad consequences can be pretty much nullified with tests. First, it's obviously a good thing because it means Infosys doesn't have to stick to using bad code (well, it obviously has to stick with however good the code I create is but the point is that if I know some designs or ideas to be better than the existing Infosys code, I'll just change it). As a consequence, using Infosys is now easier and more consistent than just two weeks ago. On the bad side, I've completely broken Infosys many times over during the last two weeks. If I didn't at the same time implement unit tests for all the things I change, I'd have no idea if I was introducing bugs in the code while refactoring it.

So, what I've been doing is creating tests every time I've changed code. When refactoring the database class I created a database test class to go along with it, so I'd know that it would still work the same way, even if the insides changed radically. At times this approach created some minor problems - I was refactoring one class when I had to stop that in order to refactor a dependency. The end effect is truly awesome though. So far I've 154 tests done and can be fairly certain that if something underlying or unexpected changes in the tested classes, I'll know about it as soon as I run the tests - which happens everytime I change code.

Better still, in case someone else at some point decides to join in on the development of Infosys it will be a breeze to install the system and then do a systems test to make sure that everything is working as it should. If any tests break, you'll know immediately that there's a problem in the system - and you won't need to dig around for hours looking for the problem.

And of course, there's another added bonus: while writing tests for the Infosys code, I've come across things that weren't quite as I would expect them to be. In other words, writing tests have helped me fix bugs I didn't even know I had and that didn't get picked up in the code. One particularly nasty bug went something like this:

```
...
$select->setWhere(field, =, value);
return $this->findBySelectMany($select);
...
```

There's nothing wrong as such with the above code - in fact, it hasn't changed at all. However, the underlying code worked in a way that made the above code dangerous. Specifically, if something was bad with the input, the function would fail with a bool return code. Which the above code obviously just ignores. I had no idea this code was flawed until I created a test to check it. Now, however, the underlying code throws exceptions if the input is bad - no longer any way for code to ignore errors silently.

### 5. Keep it together
Refactoring Infosys has been a ton of fun so far, I've learned a lot through it. However, while doing it it's important to keep in mind that there's a reason for doing this: refactoring in itself is pointless, it has to be done to achieve something. For Infosys this something is the need to be able to implement a greater range of features and do so more easily (the issue list on GitHub is fairly long). If I didn't try to attain a specific goal, I would fall into the trap of the DIY person: there's always more to do and it can always be done better. Sooner or later you'll tire of your neverending project and put it aside. This is something to avoid and the way to do that is create clear goals and attain them one by one. Then you can measure your progress and you will actually stand a chance of getting done.
