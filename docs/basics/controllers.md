# Controllers

See [WPEmerge controllers documentation](https://docs.wpemerge.com/#/framework/routing/controllers)

# Create Controller

To create controller class run in your terminal

```sh
php brocooly new:controller ControllerName
```

This will create `ControllerName.php` file within `Theme/Http/Controllers` directory

You may pass two options - `-c` (this will create `__construct()` method within controller) and `-r` ("resource" controller - creates two additional methods to handle singular and archive pages for post type)

## Dependency Injection

Under the hood Service Providers uses [Pimple Container](https://github.com/silexphp/Pimple). So within this objects any rule may be provided. But it's not quite powerful as [PHP DI](https://php-di.org/) therefore no autowire and wildcards. Also Brocket has no goal to override WPEmerge Controllers or Container (well maybe one day but not for now). So how do you handle DI within your controller

You may instantiate your controller within a container as it is mentioned [here](https://docs.wpemerge.com/#/framework/routing/controllers?id=instantiation)

That being said

You have controller
```php
// CustomController.php

private $posts;

public function __construct( PostsRepositoryInterface $posts )
{
    $this->posts = $posts;
}
```

and your Repository
```php
class PostsRepository implements PostsRepositoryInterface
{
    // some logic here
}
```

Now you may register this repository as an interface key and pass it into Controller params
```php
// ThemeServiceProvider.php
public function register( $container )
{

    $container[ PostsRepositoryInterface::class ] = $container->factory( function( $c ) {
        return new PostsRepository();
    } );

    $container[ CustomController::class ] = function( $c ) {
        return new CustomController( $c[ PostsRepositoryInterface::class ] );
    }
}
```