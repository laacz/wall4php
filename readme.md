Read this
---------
This project has not been updated for a while (since 2004), so, I decided to put it on github. If you are using wall4php (chances are low), or if you are willing to contribute (chances are even lower), feel free to fork and go nuts with pull requests afterwards. Original site along with downloads can be found at [wall.laacz.lv](http://wall.laacz.lv/).

What?
-----

WALL4PHP is implementation of WALL (Wireless Abstraction Library, originally created by Luca Passani for the Java platform; more info at [WURFL's site](http://wurfl.sourceforge.net/java/wall.php)) for PHP. It is NOT implementation of JSTL for PHP (though it can be done with some small tweaks). Discussion is going on at Y! group [wmlprogramming](http://groups.yahoo.com/group/wmlprogramming/).

If You use it somewhere, let me know. It is allways good to know that you are creating something that people actually use :) I will not disclose this information unless you agree with that.

Something about this THING
--------------------------

The main point of Wall is to help out developers, who create sites mainly for mobile devices by providing a simple generalized markup language.

For example, some devices support XHTML, some others WML. You might not know that, but in Japan there are Imode devices, which support something like modified HTML. It could be painful writing three versions for provide maximum statisfaction for the end-user. And problem does not end here. Some devices support some extensions for either of these languages. Some do not support some basic features. How could we know that?

From a case study. Let's assume, that you want to bold some text within a link (anchor). In WML this would look like `<a href="link">this is <b>bold</b> and this is not</a>`. And for XHTML you could write analogue code, but using `<strong>`. Where's the catch? Some devices do not support bolding text within an anchor. Furthermore - they choke, and throw an error. End user is upset. You are upset.

Well, Wall takes these questions away from you. It should be mentioned, that database, which contains each device's characteristics (preferred markup, features, and much more) is called WURFL (more info [at WURFL's site](http://wurfl.sourceforge.net/)). That is where WALL gets info about required device. Interface with WURFL (which, in fact, is a huge XML file) for PHP is provided by [PHP Tools](http://wurfl.sourceforge.net/php/index.php) in simple OOP way.

Now, WALL is an easy way to solve your problems. In a case above you now should write this way: `<wall:a href="link">this is <wall:b>bold</wall:b> and this is not</wall:a>`. WALL library takes it from here and shows XHTML to those devices, which understand it, WML to other. And even more - if a device (according to WURFL) does not support bodling text within anchors, it removes it (generated WML code is without b elements).

Just remember. This library is made for wireless devices and not browsers like Firefox or Internet Explorer. Forget it. If you want to create rich pages which make use of features which PC browsers provide, do it the right way - go nuts with XHTML 1.0, Flash, JavaScript, SVG and other sexy and rich things. If you want to create webpages which are optimized for wireless devices with limited capabilities and small screens, check out WALL. Why focusing for mobile devices? Because, if you want to create something that universal, markup would become too complicated.

Prerequisites
-------------

PHP4 or PHP5. That's it :)

Installation
------------

Unpack downloaded archive. Copy file Wall.php and directory Wall to somewhere in PHP include path. Open file wall_prepend.php in Your favorite code editor. Make appropriate changes. Allow webserver's user to write into DATADIR. See [WALL reference Guide](http://wurfl.sourceforge.net/java/refguide.php), sample files, which are provided with this archive, and You are on!

Something to note (updates)
---------------------------

Don't forget to follow changes made to [WURFL XML file](http://sourceforge.net/project/showfiles.php?group_id=55408&package_id=50315) and [php tools](http://wurfl.sourceforge.net/php/index.php) by Andrea. And, of course, WALL4PHP will be improved and changed, as time goes on...

Supported tags
--------------

* a
* alternate_img
* b
* block
* body
* br
* caller
* cell
* cool_menu
* cool_menu_css
* document
* font
* form
* h1_h6
* head
* hr
* i
* img
* input
* load_capabilities
* marquee
* menu
* menu_css
* option
* select
* title
* wurfl_device_id
* xmlpidtd

TODO
----

Here goes todo list. Or wishlist. Or however you name it.

* id and class attributes to more elements.
* [Contribution to WURFL from Murray Brandon](http://wurfl.sourceforge.net/java/contributions/brandon.php)
* [Murray's versions extension by Nicholas Albion](http://wurfl.sourceforge.net/java/contributions/nicholas.php)
* Bring back ISO-8859-* charsets (by WALL preconfig?)
* Have a go at non-WALLish extensions (as always - people want bling bling and stuff)
* As project has gained some popularity, it is time to break it by refactoring (lack of error messages drives everyone nuts; [for example](http://wurfl.sourceforge.net/utilities/wallify.php))
