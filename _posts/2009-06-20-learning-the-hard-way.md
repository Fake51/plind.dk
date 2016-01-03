---
published: true
category: blog
layout: default
---

Setting up a wordpress blog is not hard, even if you're tired and enjoying a very nice glass of Laphroaig. Remembering your new admin password, on the other hand, can be very hard in that situation. So what to do when you have forgetten it? You could try having your wordpress install reset it, but if you're as lucky as me it doesn't actually send you the new password (or the activation key before that). You could also try to install this: [http://www.village-idiot.org/archives/2007/05/22/wp-emergency-password-recovery/](http://www.village-idiot.org/archives/2007/05/22/wp-emergency-password-recovery/) if you have access to the code and you trust it (I would). However, if you're a bit more in the debugging spirit and you can mess with the code, you could also have a look round the wordpress script files for interesting functions. In particular, the **wp_check_password** function in _wp-includes/pluggable.php_ checks if a password matches the input - stick a <strong>return true;</strong> in the beginning of the function and you'll be flying by with any character jumble used for password.

... remember to remove this hack or you WILL regret it.