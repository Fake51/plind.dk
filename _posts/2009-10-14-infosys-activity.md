---
published: true
layout: default
category: blog
---

Lately I've been working a lot on the Infosys code, so I figured I'd post some updates I've done here.

* Refactored core code:
 * Turned singletons into normal classes, using static variables for shared data if needed
 * Introduced exceptions instead of bool return values in many places
 * Changed framework/MVC integration to be based on dependency injection
 * Changed output to use template files rather than huge view classes
 * Refactored request handling to use objects and delegate work more
 * Improved the database class to use more proper functions with better sanity checks
* Wrote tests
 * So far have 206 tests spread over framework classes, MVC models and data entities
* Delegated work to 3rd parties
 * Infosys now uses SwiftMailer and PHPTAL
* Implemented a database migration tool

A whole lot more is planned - [see the issues list for details](http://github.com/Fake51/Infosys/issues). There are also some more abstract things that might happen, such as turning Infosys into a more modular system - the reasoning behind this being that other cons have shown a bit of interest in using the system but would probably need it modified, yet don't have massive amounts of time for modifications, etc. The nature of Infosys should make it fairly easy to generate an install tool that allows for choosing which parts of the system are active plus making other customizations.