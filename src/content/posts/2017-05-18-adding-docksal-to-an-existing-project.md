---
layout: post
title: "Adding Docksal to an existing project"
categories: [drupal]
tags: [docksal,drupal 8,local environment,devops,]
---

## Adding docksal to your project

You can add a `.docksal` to the root for your project or you can also
run `fin config generate` that command will create the folder and a
couple of files for you:

```
├── .docksal
│   ├── docksal.env
│   └── docksal.yml
```

You can copy the config for the .env file from my previous post and adjust it
to your needs, like changing the lock for versions you might need or the
`VIRTUAL_HOST` name, etc.

If for some reason your project is not using a docroot (a folder where
  your drupal code should live) you can modify this line from the
  docsal.yml file:

  ```
  environment:
  - APACHE_DOCUMENTROOT=/var/www
  ```

  You should try to always have a docroot folder, but this is how you can
  override docksal default config to load your site from your project's
  root.

  Remember to set your `settings.local.php` to load your database from your
  container now:


```php
$databases = array();

$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'default',
  'username' => 'user',
  'password' => 'user',
  'host' => 'db',
  'prefix' => '',
);
```


Import your database with

  `fin db import ~/dump.sql`

  `fin start`

  `fin drupal cr all`
