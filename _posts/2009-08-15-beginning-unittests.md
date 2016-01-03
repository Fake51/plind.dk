---
category: blog
layout: default
published: true
title: Beginning unittests
---
I've lately started implementing unit-tests for the BeWelcome codebase and from the get-go it's been a joy. Well, almost. Spent a little time figuring out how to get the framework working and making it accessible, but after that's it's been sweet. Just running the tests gives a basic sort of joy, seeing the dots come up one by one as the assertions are tested.

It's been interesting to get started on, though. We never used unit-tests before (there are a few starts here and there, but nothing substantial) and we never designed for it. Basically this means that there are a number of dependcncies that make it harder to write tests for the BeWelcome codebase. So far I'm just writing tests for the entities in the framework, as they are smaller, better written and have less dependencies than most of the rest of the code (well, that's how I designed them - hopefully it's the case as well).

So, as a shortcut in order to get tests working I'm instantiating the BW framework and then running the tests with dependencies. The entities depend on a DB connection and possibly other entities - and that's it. That makes them fairly easy to test without refactoring them to use dependency injection - the rest of the code might not be as easy to deal with.

Two things got me started on implementing unit-tests: at my work, we've been making an effort lately to get more tests into place. Seeing how that worked  was good inspiration. The second thing was reading the following blog post: [http://codeutopia.net/blog/2009/06/05/unit-testing-introduction/](http://codeutopia.net/blog/2009/06/05/unit-testing-introduction/) - it's the first part of a good series on doing unit-tests. After reading that, the documentation for [PHPUnit](http://www.phpunit.de/) was quite helpful as well.

The one bit of trouble I had was creating test-suites to group tests together. The PHPUnit documentation wasn't worth a whole lot (see [Chapter 7](http://www.phpunit.de/manual/current/en/organizing-tests.html)) as although it does mention the various ways of creating test suites, it doesn't give any examples of putting several tests in a a testsuite. In the end, I went with the addTestFile method of testsuites to get things working - this spared me trouble of requiring the test files as well.

Should anyone have the same trouble as me, here's my method:

```
require_once("PHPUnit/Framework.php");

class EntityTests extends PHPUnit_Framework_TestSuite
{
    public static function suite()
    {
        $suite = new EntityTests;
        $suite->addTestFile('GroupEntityTest.php');
        $suite->addTestFile('GroupMembershipEntityTest.php');
        $suite->addTestFile('MemberEntityTest.php');
        return $suite;
    }
}
```
