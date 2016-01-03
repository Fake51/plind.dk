---
published: true
category: blog
layout: default
---

I can't ever recall the magic methods of PHP and sometimes google doesn't seem to display them as the first couple of hits, so for my own sake, a cheat-sheet of them with a few notes:

* **__construct(/* arguments */)** : called on construction of the object
* **__destruct()** : called when the object is destroyed
* **__get($var)** : called when the object does NOT contain the property $var or when $var is private or protected. ALSO called on empty($object-&gt;$var) if $var is private or protected
* **__set($var, $value)** : called when the object does NOT contain the property $var or when $var is private or protected.
* **__call($method, $args)** : called when the object does NOT contain the method $method. Unlike __get and __set it doesn't hide private or protected methods.
* **__callStatic($method, $args)** : equivalent to __call but for static calls. New as of 5.3.
* **__clone()** : called on clone object.
* **__isset($var): ** when empty() or isset() is called on a non-existing or private or protected property.
* **__unset($var)** : called on unset when object does NOT contain $var or $var is private or protected.
* **__sleep()** : called if serialize() is called on the object. Should return array of properties that will be serialized before the object is destroyed. Allows object to do cleanup before serialization.
* **__wakeup()** : called if unserialize() is called on the object. Notifies the object that it is being brought back to life.
* **__toString()** : let's a class decide what to return if it is converted to string.
* **__invoke(/* arguments */)** : called if the objectt is called as a function. New as of 5.3
* **__set_state($array)** : static method called     for classes exported by var_export.

And, the [manual page describing the methods](http://us3.php.net/manual/en/language.oop5.magic.php).
