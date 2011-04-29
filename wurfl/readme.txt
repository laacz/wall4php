All the software described here was developed by Andrea Trasatti and is being
distributed for free to be use with WURFL.
Multicache implementation by Herouth Maoz ( herouth AT spamcop DOT net )
If you have comments or requests, don't hesitate to contact me:
atrasatti AT users DOT sourceforge DOT net

================
Revision history
================

V2.1 Beta2
- new search algorithm for unknown user agents, much faster
- added wurfl_config.php for cleaner configuration parameters
- added check_wurfl.php as a sample script to make sure everything is ok and
	read info about a device
- added update_cache.php, new script to create a new cache file (cache.php
	or multicache) so that you can force cache updates in production 
	environments and avoid automatic refresh which might cause problems on 
	sites with high traffic
- fixed a bug in case cache was set to false and WURFL_AUTOLOAD to true
- log levels are now supported appropriately

V2.1 Beta
- new licence (MPL 1.1)
- multicache

V2.0
- new license (BSD)
- better cache

=============
WARNING
=============
The current wurfl_class.php IS NOT compatible with the web patch.
From the very beginning the wurfl_class had a little check for a set of strings
in the user agent. If found, the browser is considered a WEB browser and the
class doesn't even search for it in WURFL. This was useful (and gives better
performances) for the 'browser_is_wap' property.
This is will be configurable in future versions.
A temporary solution could be to remove the check and let the class search for
the web browser. WURFL has a few defaul browsers and if the "web patch" is 
applied you will have a good number of web browsers recognized properly.

=============
WURFL parser
=============

This is a VERY simple PHP script to demonstrate how you could parse the WURFL
and have an associative array. Once you have the array you can obtain all the
data you need. You will certanly need some filters and take in consideration,
mainly, the fall_back feature. It's not natively implemented here, that is to
say, if you don't know a phone's feature and it's not listed in its
characteristics, you (read "your software") will need to search in its
parent.

2 caching systems are available. "classic" cache generates a file called
cache.php (you can change it in your config) which stored the entire associative
array serialized (old PHP versions) or using var_export (PHP 4.3.4 and above).
New "multicache" allows you to store only basic info in cache.php and generating
many tiny files in a specific directory. This should provide much faster access
times when reading the capabilities of a new device.
NOTE: if wurfl's mtime is older than the one stored in the cache file I
will not update the cache.

Defines used by this library:
WURFL_CONFIG      boolean, means you really configured the lib, otherwise quit
WURFL_FILE        string, Full path and filename of wurfl.xml
WURFL_USE_CACHE   boolean, true if I want to use a cache file
CACHE_FILE        string, with full path and filename of the cache file to
                  use
MULTICACHE_DIR    string, used only if you enabled Multicache, defines where
                  the cache files will be stored. WARNING: while cache.php will
                  grow in size but remain a single file, here the files will
                  grow in number. Expect more than 5000 tiny files.
MULTICACHE_SUFFIX string, suffix for the files generated using Multicache.
                  Useful if you use a caching system and don't want to load your
                  shared memory with a ton of tiny files.
WURFL_CACHE_AUTOUPDATE  boolean, tells the class to automatically update the
                        cached files with a new XML is found. This is NOT
                        suggested when using MULICACHE because of the high
                        number of files to be updated.  Race conditions are
                        highly possible to happen.

WURFL_PATCH_FILE  string, optional patch file for WURFL
WURFL_AGENT2ID_FILE  string, used by wurfl_class.php. needs to be removed
                     when a new WURFL is found

WURFL_LOG_FILE    string, defines full path and filename for logging
WURFL_AUTOLOAD    boolean, true if you want the XML to be loaded at every
                  startup. If not, the XML will be loaded when needed.
LOG_LEVEL         integer, desired logging level. Use the same constants
                  as for PHP logging

Logging. Logging is not really part of the tasks of this library. I included
basic logging just to survive BIG configuration mistakes. Warnings and
errors will be logged to the WURFL_LOG_FILE or, if not present, in the
webserver's log file using the error_log function.
Really bad events such as a wrong XML file, missing WURFL and a few more
will still generate a die() along with a log to file.
Someone might also want to send an e-mail or something, but this is WAY
ahead of the scope of this script.


=============
WURFL class
=============

This script is intended to help you to access data stored in WURFL. The class
works on an array generated by the WURFL parser. You may implement your own
parser emulating the same data structure if you'd like.

To configure the class you will need to adapt the following defines to your
environment:

WURFL_CONFIG		used in other libraries if the configuration was done
DATADIR           Where all data is stored (wurfl.xml, cache file, logs, etc)
WURFL_FILE        string, Full path and filename of wurfl.xml
WURFL_USE_CACHE   boolean, true if you want to use a cache file (strongly
                  suggested)
CACHE_FILE        string, with full path and filename of
                  the cache file to use (refreshed when a new WURFL is found)
WURFL_PATCH_FILE  string, optional patch file for WURFL
WURFL_AGENT2ID_FILE   string, used by wurfl_class.php. Is refreshed when a new
                      WURFL is found
WURFL_LOG_FILE    string, defines full path and filename for logging
WURFL_AUTOLOAD    boolean, true if you want the XML to be loaded at every
                  startup. If not, the XML will be loaded when needed.
MAX_UA_CACHE      int, set the maximum number of user_agents to cache in agent2id
LOG_LEVEL         int, sets the logging level. Use default PHP logging defines

The class loads the parser automatically. Use WURFL_PARSER_FILE as a define to
store the full path and filename. If not defined, the class will try to load the
parser in the local directory.

The class is initialized calling the constructor, wurfl_class and passing two
variables that may be empty. The former is the full XML parsed (like the parser
does) and the latter is the array of user agents and id's as generated by the
parser. Pass two empty variables if you want them to be filled (when needed) or
the real values if you already have them. The class will check the values and
the cache files and decide what to do. Also check your configuration, because
the behaviour will change.

Use the public method GetDeviceCapabilitiesFromAgent() passing the user agent
to make the class search for the best fit and fill the object's properties.
Once again, what the class does depends on your configuration, if you enabled
cache files and so on.
Once you have instantiated the object and passed a user agent you may use all
the class' methods.
Here is a list of properties:

$wurfl_class->_wurfl          is the WURFL array (all of it)
$wurfl_class->_wurfl_agents   is the associative array made of the user agents
                              and unique id's
$wurfl_class->user_agent      the visitor's user agent
$wurfl_class->wurfl_agent     the WURFL's best fitting user agent
$wurfl_class->id              the corresponding id
$wurfl_class->GUI             true if the device supports Openwave's GUI
                              extensions
$wurfl_class->browser_is_wap  true if the device is WAP capable (web browsers
                              such as Opera are considered non-WAP 
                              even if they DO support WML)
$wurfl_class->capabilities    the array of device's capabilities

Note: PHP (up to version 4.3) does not have any distinction between private and
public methods. In the wurfl_class implementation, I named all private methods
with a leading underscore to distinguish them from public methods without the
underscore. If you are interested in knowing the details, just open the class,
there are beautiful JAVADOC comments for each variable and method.
These libraries don't require register_globals anymore (from version 2 and up),
but will not work with versions before 4.1.

wurfl_class($wurfl, $wurfl_agents)   is the constructor. Built to work
                                     best with the wurfl_parser.

GetDeviceCapabilitiesFromAgent($ua)  given a user agent it will search WURFL
                                     for the best fit

getDeviceCapability($capability)     given a capability it will tell you the
                                     value. Remember that capabilities might be
                                     string, integer or boolean.

Extra features in wurfl_class: after some tests we noticed that the biggest
bottleneck was the hunt for the unique id for unrecognized devices. The solution
was, once again, an extra caching system that would store the associations
between user agents and wurfl IDs. This is valuable when an unknown agent visits
your site and you need to seek the most similar one in all the agents listed in
WURFL. Once a new agent has visited your site, you have a direct referral to it
and there is no need to search for it again.
 
A logging method is implemented too. This function will log the class actions
and calls. This feature is used mostly for debugging. 

A little note about the method to find the device, I have implemented a simple
system to avoid searching for user agents that are web browsers. The check is
made in the method "GetDeviceCapabilitiesFromAgent", if you have a strange
user agent that is not being recognized properly, check the logs see if the
class *thinks* it's a web browser, if so, 99% it's that specific check. Change
it at your wish and maybe let me know.

================
wurfl_config.php
================
Use this file to configure the parameters needed. Change the ones you like,
defaults should be good for most users.
Make sure the webserver has the permissions needed.

================
update_cache.php
================
High load sites might like to use this script to update the cache and set the
WURFL_CACHE_AUTOUPDATE to false and force it when needed. This is especially
useful with the multicache to avoid race conditions.
A simple locking system is implemented.

================
check_wurfl.php
================
Use this script to make sure you have configured the library appropriately and
to read devices' capabilities, when you need to.

=============
NOTICE
=============

Due to the HIGH load of reading the cache.php file every time the system needs
to search for a new device it is HIGHLY reccomended to use a caching system
such as Zend Cache, APC Cache version 2, Turck cache or eAccelerator.
If you don't use a caching system it will take a LONG time to load the file
cache.php that currently grows up to more than 2MB!

If you don't want to use any PHP cache, multicache should work well without.

=============
Sample code
=============

A demo page:

<?php

require_once('./wurfl_config.php');
require_once(WURFL_CLASS_FILE);

// creating the WURFL object
$myDevice = new wurfl_class($wurfl, $wurfl_agents);
$myDevice->GetDeviceCapabilitiesFromAgent($_SERVER["HTTP_USER_AGENT"]);
if ( $myDevice->browser_is_wap ) {
  header("Content-Type: text/vnd.wap.wml");
  echo '<?xml version="1.0" encoding="ISO-8859-1"?>'."\n";
?>
<!DOCTYPE wml PUBLIC "-//WAPFORUM//DTD WML 1.1//EN" "http://www.wapforum.org//DTD//wml_1.1.xml">
<wml>
 <card>
  <p mode="nowrap">

<?php
  if ( $myDevice->getDeviceCapability('gif') ) {
    echo '<img src="logo.gif" alt="Global TEL" />'."\n";
  } else {
    echo '<img src="logo.wbmp" alt="Global TEL" />'."\n<br/>\n";
  }
?>

<a href="index.php">Home</a><br/>
 </p>
 </card>
</wml>

<?php
} else {
?>
<img src="logo.gif"><br><br>
Welcome Web browser.<br>
We are sorry, but we are only offering WAP services, at this time.<br>
<?php } ?>



More information can be found on the website or on the mailinglist wmlprogramming.
http://wurfl.sf.net/
http://groups.yahoo.com/group/wmlprogramming

$Id: readme.txt,v 1.5 2005/05/03 14:44:54 atrasatti Exp $
