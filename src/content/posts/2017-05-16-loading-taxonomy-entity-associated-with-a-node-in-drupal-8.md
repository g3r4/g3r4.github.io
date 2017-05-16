---
layout: post
title: "Loading a taxonomy entity associated with a node by term id in Drupal 8"
subtitle: "From a block using dependency injection"
categories: [drupal]
tags: [drupal 8,dependency injection,taxonomy term,entity storage,node load, drupal console]
author: "Gerardo"
---

## Creating a new block to load the node's attached tid from

We can start by using the **wonderful** Drupal Console to create our new block:

`drupal generate:plugin:block`

If you want to use the interactive method, these are the settings you
should enter for this block:

```
// Welcome to the Drupal Plugin Block generator
Enter the module name [address]:
> custom_module

Enter the plugin class name [DefaultBlock]:
> TaxonomyTermLoadBlock

Enter the plugin label [Taxonomy term load block]:
>

Enter the plugin id [taxonomy_term_load_block]:
>

Enter the theme region to render the Plugin Block. [ ]:
>

Do you want to load services from the container (yes/no) [no]:
> yes


Type the service name or use keyup or keydown.
This is optional, press enter to continue

Enter your service [ ]:
> current_route_match
Enter your service [ ]:
> entity.manager
Enter your service [ ]:
>

You can add input fields to create special configurations in the block.
This is optional, press enter to continue

Do you want to generate a form structure? (yes/no) [yes]:
> no


Do you confirm generation? (yes/no) [yes]:
> yes
```

This will create a new block plugin for us:

`modules/custom_module/src/Plugin/Block/TaxonomyTermLoadBlock.php`

For this to work, you should have a previously created module, if you haven't
created one yet, you can generate it with Drupal Console as well:

`drupal generate:module`

## Tweaking the generated code

Since we should try to use interfaces for dependency injection instead of
classes, let's change this a little bit, first add these use statements:

```php
use Drupal\Core\Routing\ResettableStackedRouteMatchInterface;
use Drupal\taxonomy\TermStorageInterface;
```

And change the signature arguments on the `__construct` method:

```php
public function __construct(
      array $configuration,
      $plugin_id,
      $plugin_definition,
      ResettableStackedRouteMatchInterface $current_route_match,
      TermStorageInterface $entityManager
)
```

The create method:

```php
public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
  return new static(
    $configuration,
    $plugin_id,
    $plugin_definition,
    $container->get('current_route_match'),
    $container->get('entity.manager')->getStorage('taxonomy_term')
  );
}
```

And our build function should look like this:

```php
// Get current node
    $current_node = $this->currentRouteMatch->getParameter('node');
    if ($current_node != null) {
      // Loads from a taxonomy reference field: field_taxonomy_term defined on our node
      if ($current_node->hasField('field_taxonomy_term')) {
        if (!empty($current_node->get('field_taxonomy_term')->target_id)){
          $term_id = $this->entityManager->load($current_node->get('field_taxonomy_term')->target_id);
          $taxonomy_term_name = $term_id->getName();
        }
      }
    }

   $build = [];
   $build['taxonomy_term_load_block']['#markup'] = "<h1> $taxonomy_term_name </h1>";

   return $build;
```

Clear your caches after tweaking this file `drupal cr all`

Now you can go navigate to `/admin/structure/block` on your site, click the
**Place block** button on the region you would like to see your block and go
ahead and visit a node that you already have created with a taxonomy term
attached to it.

This is the full `TaxonomyTermLoadBlock.php` file:

```php
<?php

namespace Drupal\custom_module\Plugin\Block;

use Drupal\Core\Block\BlockBase;
use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\Core\Routing\ResettableStackedRouteMatchInterface;
use Drupal\taxonomy\TermStorageInterface;

/**
 * Provides a 'TaxonomyTermLoadBlock' block.
 *
 * @Block(
 *  id = "taxonomy_term_load_block",
 *  admin_label = @Translation("Taxonomy term load block"),
 * )
 */
class TaxonomyTermLoadBlock extends BlockBase implements ContainerFactoryPluginInterface {

  /**
   * Drupal\Core\Routing\CurrentRouteMatch definition.
   *
   * @var \Drupal\Core\Routing\CurrentRouteMatch
   */
  protected $currentRouteMatch;
  /**
   * Drupal\Core\Entity\EntityManager definition.
   *
   * @var \Drupal\Core\Entity\EntityManager
   */
  protected $entityManager;
  /**
   * Construct.
   *
   * @param array $configuration
   *   A configuration array containing information about the plugin instance.
   * @param string $plugin_id
   *   The plugin_id for the plugin instance.
   * @param string $plugin_definition
   *   The plugin implementation definition.
   */
  public function __construct(
        array $configuration,
        $plugin_id,
        $plugin_definition,
        ResettableStackedRouteMatchInterface $current_route_match,
        TermStorageInterface $entity_manager
  ) {
    parent::__construct($configuration, $plugin_id, $plugin_definition);
    $this->currentRouteMatch = $current_route_match;
    $this->entityManager = $entity_manager;
  }
  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
    return new static(
      $configuration,
      $plugin_id,
      $plugin_definition,
      $container->get('current_route_match'),
      $container->get('entity.manager')->getStorage('taxonomy_term')
    );
  }
  /**
   * {@inheritdoc}
   */
  public function build() {
// Get current node
    $current_node = $this->currentRouteMatch->getParameter('node');
    if ($current_node != null) {
      // Loads from a taxonomy reference field: field_taxonomy_term defined on our node
      if ($current_node->hasField('field_taxonomy_term')) {
        if (!empty($current_node->get('field_taxonomy_term')->target_id)){
          $term_id = $this->entityManager->load($current_node->get('field_taxonomy_term')->target_id);
          $taxonomy_term_name = $term_id->getName();
        }
      }
    }
    $build = [];
    $build['taxonomy_term_load_block']['#markup'] = "<h1> $taxonomy_term_name </h1>";

   return $build;
  }

}

```
