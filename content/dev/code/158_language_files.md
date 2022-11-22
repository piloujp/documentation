---
title: Language Files - Developer Information on Array based Language files
description: Structure of Language Packs for Zen Cart 1.5.8 and above 
weight: 10 
layout: docs
---

In Zen Cart 1.5.8, the language file structure was modified so that the PHP `define` statement is no longer used directly to set a language constant.  The reason for this is that newer versions of PHP will emit warning messages when a constant is redefined (in an override vs core file), so the old approach would create debug logs if something wasn't done.

The new design builds an array of language definitions which are built into constants once all of them are read.  For this reason, the new files are called *array based language files* (as opposed to the older *define based language files*). 

So for example, in Zen Cart 1.5.7, the file `includes/languages/english/login.php` would have: 

```
define('HEADING_TITLE', 'Welcome, Please Sign In');
```

In Zen Cart 1.5.8, the file `includes/languages/english/lang.login.php` would have: 

```
$define = [
...
    'HEADING_TITLE' => 'Welcome, Please Sign In',
... ]; 

```

with a `define` to be done later after all the strings had been gathered.

Using arrays allows for the following kinds of behavior: 

- Create a set of language definitions in the storefront override files, but run the base file to ensure that any missing definitions are created, while not updating any definitions that are changed in the override.

- Create a definition in the main language file which works for most cases, but allow it to be overridden in a per-page language file. 

In the process of doing this work, the language files were reviewed for duplicates, and consolidation was done where appropriate to reduce the burden on translators. 

### Handling back references within a file 

In Zen Cart 1.5.7 and below, structures like the following were possible: 

```
  define('TEXT_GV_NAME','Gift Certificate');
...
  define('BOX_INFORMATION_GV', TEXT_GV_NAME . ' FAQ');
```

In 1.5.8, you must create array elements which reference earlier array entries 
*after* creating the entire `$define` array. 

```
$define = [
...
    'TEXT_GV_NAME' => 'Gift Certificate',
...
]; 
$define['BOX_INFORMATION_GV'] = $define['TEXT_GV_NAME'] . ' FAQ';
```

This example is taken from `includes/langauges/lang.english.php`.

### Handling back references between files 

Prior to Zen Cart 1.5.8, the `define` operations were done incrementally, so a language file which was loaded later could refer to one loaded earlier. 

For example, a file in `includes/languages/english/extra_definitions/` could do this: 
```
define('ABOUT', '<li><a href="' . zen_href_link(FILENAME_ABOUT_US) . '">' . BOX_INFORMATION_ABOUT_US . '</a></li>');
```

This is no longer permitted; the constant must be replaced by a string. 

```
define('ABOUT', '<li><a href="' . zen_href_link(FILENAME_ABOUT_US) . '">' . 'About Us' . '</a></li>');
```

### Related: 
- [Language Files - New vs Legacy in 1.5.8](/dev/code/158_order_language_files/)
- [User information on Array based Language files](/user/localization/158_language_files/)
- [Creating a Language Pack for Zen Cart 1.5.8 and above](/dev/languages/creating_a_language_pack/) 
