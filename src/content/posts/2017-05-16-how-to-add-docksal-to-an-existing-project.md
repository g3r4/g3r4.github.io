---
layout: post
title: "How to add Docksal to an existing project"
subtitle: "With a practical example"
categories: [drupal]
author:     "Gerardo"
header_img:
  url: "assets/img/containers.jpg"
  author: "Boba Jovanovic"
---

## What is Docksal?

[Docksal](http://docksal.io/) is container based technology stack tool to work with your Drupal projects locally, I've used a lot of tools in the past,
the most recent one being [DrupalVM](https://www.drupalvm.com/), which is an **amazing** project that I still
use from time to time, I've also installed a MAMP stack locally (not to be confused with the MAMP tool), and a few others like
Kalabox and Acquia Dev Desktop.

All these tools fulfil their purpose, a lot of people get used to one thing and do not want to experiment or deal
with another tool that they have to master just to have their local setup so they can start to really work.

If you would like to have consistency in your local setups for projects and just want something fast that works,
I think you should give Docksal a try, after you learn the basic commands, it is very easy to use and deploy for
a different number of projects.

## Creating a new Drupal project

We will be using composer for this step, if you don't have it, you can download it from here: [Get Composer](https://getcomposer.org/download/)

Once you have composer installed locally, you can create a new project using [Drupal Composer](https://github.com/drupal-composer/drupal-project): 

```bash
composer create-project drupal-composer/drupal-project:8.x-dev some-dir --stability dev --no-interaction
```

That command should get the latest Drupal 8 version and all its dependencies.

## Installing Docksal

The requirements for Docksal [are pretty straight-forward](http://docksal.readthedocs.io/en/master/getting-started/env-setup/),
if you are using MacOS or Windows, you will need VirtualBox and execute the commands in the link above, this is because
at the time of writing this post, only Linux supports running containers natively, in the case of the other OSs a thin VM
is required to run the docker containers.

[The official setup documentation](http://docksal.readthedocs.io/en/master/getting-started/setup/#setup-instructions), 
suggests that we clone a Drupal 8 project from their [Github repository](https://github.com/docksal/drupal8), I do not
like this idea a lot because that Drupal 8 code might be outdated, and what we would like to do is to add Docksal 
to our projects, that is why we are creating one for ourselves, however, we will use a couple of files from that
repository as a base for our project.

Go ahead and create a .docksal folder inside the Drupal Project we just created, you should have a folder structure 
like this:

```
drwxrwxr-x  7 user user   4096 May 15 22:49 ./
drwxrwxr-x  8 user user   4096 May 15 22:31 ../
-rw-rw-r--  1 user user   2315 May 15 22:31 composer.json
-rw-rw-r--  1 user user 212906 May 15 22:33 composer.lock
drwxrwxr-x  2 user user   4096 May 15 22:49 .docksal/
drwxrwxr-x  2 user user   4096 May 15 22:31 drush/
-rw-rw-r--  1 user user    418 May 15 22:31 .gitignore
-rw-rw-r--  1 user user  18046 May 15 22:31 LICENSE
-rw-rw-r--  1 user user    481 May 15 22:31 phpunit.xml.dist
-rw-rw-r--  1 user user   5886 May 15 22:31 README.md
drwxrwxr-x  3 user user   4096 May 15 22:31 scripts/
-rw-rw-r--  1 user user   1502 May 15 22:31 .travis.yml
drwxrwxr-x 42 user user   4096 May 15 22:33 vendor/
drwxrwxr-x  7 user user   4096 May 15 22:33 web/

```

Inside of your `.docksal` folder, create a new file named `docksal.env` with the following contents:

```
# This is a shared configuration file that is intended to be stored in the project repo.
# To override a variable locally:
# - create .docksal/docksal-local.env file and local variable overrides there
# - add .docksal/docksal-local.env to .gitignore

# Use the default Docksal stack
DOCKSAL_STACK=default

# Lock images versions for LAMP services
# This will prevent images from being updated when Docksal is updated
WEB_IMAGE='docksal/web:1.0-apache2.2'
DB_IMAGE='docksal/db:1.0-mysql-5.5'
CLI_IMAGE='docksal/cli:1.2-php7'

# Docksal configuration.
VIRTUAL_HOST=drupal8.docksal
DOCROOT=web

# MySQL settings.
# MySQL will be exposed on a random port. Use "fin ps" to check the port.
# To have a static MySQL port assigned, copy the line below into the .docksal/docksal-local.env file
# and replace the host port "0" with a unique host port number (e.g. MYSQL_PORT_MAPPING='33061:3306')
MYSQL_PORT_MAPPING='0:3306'

# Enable/disable xdebug
# To override locally, copy the two lines below into .docksal/docksal-local.env and adjust as necessary
XDEBUG_ENABLED=0
```

If you are on Windows or MacOS and already ran `fin vm start`, you can proceed, if not, run it now.

Go back to your project's root and run `fin start` you should see something like this:

```
Starting services...
Creating network "drupal8_default" with the default driver
Creating volume "drupal8_project_root" with local driver
Creating volume "drupal8_host_home" with local driver
Creating drupal8_cli_1
Creating drupal8_db_1
Creating drupal8_web_1
Connected vhost-proxy to "drupal8_default" network.
```

After that, you can open your browser and go to [drupal8.docksal](drupal8.docksal) and you will see the 
Drupal 8 installation page, however, if you want to quickly install Drupal without having to deal with
the GUI, you can use this Drupal Console command, go to your project's web folder and run :

```
cd web
fin drupal site:install  standard --langcode="en" --db-type="mysql" --db-host="db" --db-name="default" --db-user="user" --db-pass="user" --db-port="3306" --site-name="Drupal 8 Site Install" --site-mail="admin@example.com" --account-name="admin" --account-mail="admin@example.com" --account-pass="admin" --no-interaction
```

Congratulations ! you've added Docksal with basic configuration to an existing project !

Don't forget to read [Docksal's basic commands](http://docksal.readthedocs.io/en/master/fin/fin/).

## Troubleshooting

If you already have DrupalVM or any other vagrant machine, you might have an issue when running `fin vm start`, if this 
happens to you, try to delete your `/etc/exports` file and run `fin vm start` or `fin vm restart` again, this file gets 
created each time you start your VM, likewise, if you want to run your DrupalVM after playing with Docksal, you need
to remove that file before you run `vagrant up`.

At the time of writing this, you might have this issue when you run`fin drupal`:

```
fin drupal --version
WARNING: No swap limit support
[16-May-2017 03:22:26 UTC] PHP Fatal error:  Uncaught Error: Call to undefined method Drupal\Console\Core\Utils\DrupalFinder::getVendorDir() in /var/www/vendor/drupal/console-core/src/Utils/DrupalFinder.php:24
Stack trace:
#0 /var/www/vendor/drupal/console/bin/drupal.php(37): Drupal\Console\Core\Utils\DrupalFinder->locateRoot('/var/www')
#1 phar:///usr/local/bin/drupal/src/Utils/Launcher.php(30): include_once('/var/www/vendor...')
#2 phar:///usr/local/bin/drupal/bin/drupal.php(86): Drupal\Console\Launcher\Utils\Launcher->launch('/var/www', Object(Composer\Autoload\ClassLoader))
#3 phar:///usr/local/bin/drupal/bin/drupal(3): require('phar:///usr/loc...')
#4 /usr/local/bin/drupal(10): require('phar:///usr/loc...')
#5 {main}
  thrown in /var/www/vendor/drupal/console-core/src/Utils/DrupalFinder.php on line 24

Fatal error: Uncaught Error: Call to undefined method Drupal\Console\Core\Utils\DrupalFinder::getVendorDir() in /var/www/vendor/drupal/console-core/src/Utils/DrupalFinder.php:24
Stack trace:
#0 /var/www/vendor/drupal/console/bin/drupal.php(37): Drupal\Console\Core\Utils\DrupalFinder->locateRoot('/var/www')
#1 phar:///usr/local/bin/drupal/src/Utils/Launcher.php(30): include_once('/var/www/vendor...')
#2 phar:///usr/local/bin/drupal/bin/drupal.php(86): Drupal\Console\Launcher\Utils\Launcher->launch('/var/www', Object(Composer\Autoload\ClassLoader))
#3 phar:///usr/local/bin/drupal/bin/drupal(3): require('phar:///usr/loc...')
#4 /usr/local/bin/drupal(10): require('phar:///usr/loc...')
#5 {main}
  thrown in /var/www/vendor/drupal/console-core/src/Utils/DrupalFinder.php on line 24

```

This is because the latest version Drupal Console (RC19) needs `webflo/drupal-finder:^0.3.0` which is already installed
when you created your Drupal project using composer create-project, but the Global Drupal Console launcher might be outdated,
 in order to fix this, from your projects root, execute `fin bash` this will take you to your containers "terminal",
 try to update drupal console launcher from there with `sudo drupal self-update`, if that fails, you can follow the 
 instructions to reinstall [Drupal Console Global Launcher](https://hechoendrupal.gitbooks.io/drupal-console/content/en/getting/launcher.html)
 you might need to run those commands with sudo in order for them to properly work.