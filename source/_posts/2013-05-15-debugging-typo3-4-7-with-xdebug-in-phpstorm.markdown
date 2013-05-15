---
layout: post
title: "Debugging Typo3 4.7 with Xdebug in PHPStorm"
date: 2013-05-15 23:17
comments: true
categories:
- Backend
- PHP
- CMS
- TYPO3
- Debugging
- Xdebug
- PHPStorm
---

Since I'm currently working an Extbase-Extension for TYPO3 4.7, I had to debug some backend code. I didn't find anything on how to set this up with [Xdebug][1] and [PHPStorm][2], so I'll write it down here.

<!-- more -->


## Requirements ##

XDebug has to be installed and configured to work with PHPStorm, which works out fine with the [official docs][3]. I assume that Xdebug and PHPStorm are tested and working.


## Symlinks ##

Typo3 4.7 can be installed as usual, but you want to physically put the `typo3_src` folder into your webroot instead of just symlinking to it. This caused quite some headache while setting it up.

The webroot folder should look like this regarding symlinks:

```
webroot/
  typo3_src/
  t3lib > typo3_src/t3lib/
  typo3 > typo3_src/typo3/
  index.php > typo3_src/index.php
```

## PHPStorm setup ##

### Directories ###

When setting up the PHPStorm project you should choose your webroot folder as the *Content root* of your project. It would have been even better to select your plugin folder as *Content root* and only include the core sources, but there was no obvious way to get it done.

You can check this by opening the project settings in the Menu *Directories*.


### Path mappings ###

In the symlink setup above, the folders `t3lib` and `typo3` as well as the `ìndex.php` were symlinked inside the `typo3_src` folder. We don't have to make a physical copy of them that way, but we have to tell PHPStorm about it.

You can do this in the project settings, look for the submenu *Servers* inside *PHP*. You will have to setup a server now if you haven't already. Activate the "Use path mappings" option. For each path in your project, you can now specify it's absolute path by filling the column in the mappings table. Specify the absolute path inside `typo3_src` for the two symlinked directories and the symlinked `index.php`.

Set up theses mappings:

```
------------------------------------------------------
Project path > Absolute path on the server
------------------------------------------------------
/path/to/webroot > /path/to/webroot
  t3lib > /path/to/webroot/typo3_src/t3lib
  typo3 > /path/to/webroot/typo3_src/typo
  index.php > /path/to/webroot/typo3_src/index.php
```

Notice that the path for the webroot was mapped to it's own path. While testing this seemed to be necessary.


### One more mapping ###

Also check the URL mapping by navigating to *Deployment* in your project settings. You will have to create a deployment server configuration if you haven't already. Make sure, that you have a mapping for your webroot by entering your webroot's path into the `Local path` field and `/` into the `Web path on server: xyz` field.


## That's it! ##

The setup isn't too complicated, but there are enough possibilities to make something wrong, which I apparently did in almost all cases.

You can use [bookmarklets][4] and breakpoints to quickly debug your code on the fly. If you are not sure where to start, look for the `webroot/index.php` for the frontend and `webroot/typo3/index.php` to get you started. Also have a look at the *Network* tab of your devtools: a lot of requests are initiated on other endpoints like `webroot/typo3/alt_doc.php` or `webroot/typo3/mod.php` and a lot of others.

Also stay aware of the fact that the TYPO3 backend is made up of nested iFrames (which is slightly awkward...). If you want to reinitiate a request, it is probably better to right click into the concerned area and choose *Reload frame* than reloading the whole page which leaves you with the dashboard.

After writing down all of this I have to add one more thing: debugging software really is the most effective way to effectively learn, what it does. So yes, it is worth all the hassle of setting it up.


[1]: http://xdebug.org/
[2]: http://www.jetbrains.com/phpstorm/
[3]: https://www.jetbrains.com/phpstorm/webhelp/configuring-xdebug.html
[4]: http://www.jetbrains.com/phpstorm/marklets/