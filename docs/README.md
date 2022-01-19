# About

Developer-friendly framework heavily inspired by [Laravel Framework](https://laravel.com/) and based on [Timber](https://timber.github.io/docs/guides/wp-integration/) and [Wpemerge](https://wpemerge.com/) solutions for WordPress themes development with [Bedrock](https://roots.io/bedrock/) folder structure

## Why to Use It

While WordPress has basically everything under its hood but it is quite messy to work with. All these functions, different syntax, globals... 

Heavily inspired by Laravel, Brocket Framework was made to bring Laravel's elegancy into WordPress to build powerful applications with MVC structure, OOP code and twig template engine. It has no goal to override WordPress but to simplify it for developers. Simple as that

### OOP and MVC

Take a look - let's say we want to take all posts from database. How would we do that. Well simple enough

```php
// WordPress
$args = [
    'post_type'      => 'post',
    'posts_per_page' => -1,
];

$posts = new WP_Query( $args );
```

Quite good. But how does it look at Brocket?

```php
// Brocket
$posts = Post::all();
```

Laravelishly!

Brocket provides query builder methods for most cases (not only `all()`) and for ANY post types - and it will grow even more.

### Views

Brocket made with use of twig templates based on Timber package but do whatever you want - you may still use php files and even blade template engine

!> Use of blade templates hasn't been tested. It is a part of [WPEmerge blade](https://github.com/htmlburger/wpemerge-blade) engine

Brocket handles routes and requests with WPEmerge - so feel free to have a look at their [documentation](https://docs.wpemerge.com/#/framework/routing/request-lifecycle) to understand more. Brocket has a `Route` facade for routing, so instead of `App::route()` call just `Route` - like in a previous versions of WPEmerge. 

### Bedrock Folder Structure

Bedrock comes with a great more secure folder structure and custom environments types. You may want to know more about its [organization](https://roots.io/bedrock/) and why you should use it

!> As Bedrock have different folder structure and custom content and core directories some plugins may have a conflicts - especially those who has intend to write some settings into `wp-config.php` file. Rewrite this options into any configuration file and it will be ok. But still even some big plugins may have some issues

### Extended Post Types and Taxonomies

Brocket uses [extended post types](https://github.com/johnbillion/extended-cpts) package which highly improves usability of your projects - simple filters, admin columns and more simple but elegant options

### Simple Metaboxes and Theme Options

Create every metabox you need and theme options now simple as never with [CarbonFields](https://carbonfields.net/) and [Kirki Framework](https://kirki.org/) solutions. Brocket have them both included and provides Model's traits and facades to simplify its creation even more! 