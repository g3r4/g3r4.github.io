---
layout: post
title: "Removing Pantheon's 'available updates' dashboard messages"
subtitle: "When you want to manage your own composer.json file"
categories: [drupal]
author:     "Gerardo"
header_img:
  url: "assets/img/pantheon.jpeg"
---

When I started a project in Pantheon a year ago, composer was not fully supported by the platform, I have been using
composer to download modules and update Drupal, however, for this last update to Drupal 8.3.2 I could not merge 
Pantheon's D8 upstream to the one I had using composer.

I've been updating Drupal with `composer update drupal/core --with-dependencies`, effectively updating
core, I've been able to see the right Drupal version on my site, but Pantheon still shows these messages for me to 
update.

Since the entire point about using composer is to be able to rebuild your site whenever you need to, because you have
all of your required packages (Drupal modules) defined in **composer.json**, and all of your dependencies listed on your
**composer.lock** file, it should be easy to apply Pantheon's update so the message 

This time, since I wanted to "clean" the way I've been updating Drupal, I created a new Drupal 8 project on Pantheon, 
just to grab the general file structure, and be already updated.

After that, I added all my modules to the require section of my composer.json file :

```json
    "require": {
        "composer/installers": "^1.0.24",
        "wikimedia/composer-merge-plugin": "~1.3",
        "cweagans/composer-patches": "^1.6",
        "drupal/address": "^1.0@RC",
        "drupal/admin_toolbar": "^1.0",
        "drupal/media_entity_image": "^1.0",
        "drupal/paragraphs": "~1.0",
        .
        .
        .
```

After adding those dependencies I ran a `composer install` just to be sure all my dependencies were installed, however, 
since I've modified the **composer.json** directly with the dependencies I needed, my **composer.lock** is now 
outdated, to bring it back on sync, you can do 2 things `composer update` will look for the latest versions of your 
modules, this could potentially break your site, if you want to get the exact versions you have there, you should
run `composer update --lock` instead, since I was already doing some house keeping work, I went ahead and updated all
of the modules I had installed.

After doing that, I had to copy any **custom modules** I had, the **themes**, and **libraries** folders as well.

If you are experimenting with this on your local instace first. then you should clone your DB as well and change your 
"new" site's uuid (you only have to do this when you are testing your new install) run `drush config-get "system.site" uuid`
on your original site, and then `drush config-set "system.site" uuid "your-sites-uuid"` on your "new" site.

Perform any updates the new versions of your modules might have: `drush updb`

Clear the caches just in case, `drush cr all`, and check that your site is working.

If all of that worked, it means that you were able to use Pantheon's latest upstream and add your modules to the site,
however, if you were to push this code to your original site, Pantheon would still show you the update message, this is 
because we've installed extra vendor packages and our **composer.json** and **composer.lock** are now different than the 
ones that are on Pantheon's upstream.

Before committing all this code, we need use the exact thing that Pantheon has in order to get rid of that message,
Pantheon suggests performing rebase per their instructions here: 
[Apply upstram updates](https://pantheon.io/docs/upstream-updates/#apply-upstream-updates-manually-from-the-command-line-and-resolve-merge-conflicts).
I tried this method but a lot of merge conflicts were introduced, and it would've taken me a lot time to resolve them.
That's why I came with this idea: On your original site code repository run 
`git pull -Xtheirs git://github.com/pantheon-systems/drops-8.git master`, accept all of their changes, add them, commit 
and push them. This would get rid of the update messages on Pantheon's Dashboard! However in this state, your site 
most likely will be broken, the only pieces that you need to replace with the files/folders from your "new" site are:

```
|-- autoload.php
|-- composer.json
|-- composer.lock
|-- core
|-- index.php
|-- vendor
```

Replace these folders, `git add` your files, `git commit`, and `git push origin`. You will have now the latest Drupal 
version on your site and the Dashboard message would be gone.


##Disclaimer

This should be done and created using a build artifact, but if you like me were on a deadline and with no time to 
implement a build artifact, this will at least help you getting rid of those messages when you've already updated your
code to the latest Drupal 8 version.
