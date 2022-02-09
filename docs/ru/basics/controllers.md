# Контроллеры :id=controllers

Контроллеры являются посредниками между обработкой результата запроса и выдачей его на экран пользователя и служат для группировки связанных по логике запросов. Все контроллеры находятся в папке `src/Http/Controllers`

> Все методы контроллера получают как минимум два параметра - объект запроса и путь до файла, который WordPress пытается заполучить с ним, и должны возвращать либо строку, либо массив, либо объект интерфейса `Psr\Http\Message\ResponseInterface`

?> Чтобы узнать больше о контроллерах, прочтите [документацию WPEmerge](https://docs.wpemerge.com/#/framework/routing/controllers)

Чтобы создать контроллер, выполните в терминале следующую команду

```sh
php brocooly new:controller PostController
```

Указание контроллера и его метода в обработчике маршрута приходится на метод `handle()`, разделенный символом `@`. Так, например, запись

```php
Route::is_404()->handle( 'NotFoundController@index' );
```

означает, что запрос будет обработан методом `index()` контроллера `Theme\Http\Controllers\NotFoundController`

```php
class NotFoundController
{
    public function index()
    {
        return output( 'path.to.404.view' )->withStatus( 404 );
    }
}
```

> Если пространство имен контроллера не совпадает с `Theme\Http\Controllers`, Вы можете [указать иное](https://docs.wpemerge.com/#/framework/routing/defining-routes?id=namespace) при помощи метода `setNamespace( $namespace )`

!> На данный момент Brocket не работает с классами одного действия (метод `__invoke`) и не поддерживает иной синтаксис обработки методов контроллера, в отличие от Brocooly

Чтобы быстро создать контроллер с методом `__construct()`, выполните команду с флагом `-c`. Флаг `-r` позволит создать "ресурсный" контроллер, содержащий методы `index()` и `single()` для обработки архивных запросов и одиночной страницы. Флаги можно совмещать

```sh
php brocooly new:controller PostController -rc
```

## Посредник :id=middleware

Контроллеры, которые поддерживают посредники, должны следовать [двум](https://docs.wpemerge.com/#/framework/routing/middleware?id=controller-middleware) правилам:

1. Внедрять интерфейс `WPEmerge\Middleware\HasControllerMiddlewareInterface`;
2. Использовать трейт `WPEmerge\Middleware\HasControllerMiddlewareTrait`;

```php
use WPEmerge\Middleware\HasControllerMiddlewareInterface;
use WPEmerge\Middleware\HasControllerMiddlewareTrait;

class MyController implements HasControllerMiddlewareInterface {
   use HasControllerMiddlewareTrait;

   // ...
}
```

Тогда внутри конструктора можно определять посредников для всех или же определенных методов

Чтобы зарегистрировать контроллер с поддержкой посредников, нужно выполнить команду с флагом `-m`

!> Если Вы впишете оба флага `-mc` одновременно, будет создан пустой конструктор

Подробнее о том, как создавать посредники и их синтаксис, читайте в разделе Посредники

## Внедрение зависимостей :id=dependency-injection

Под капотом Brocket фреймворк использует [контейнер Pimple](https://github.com/silexphp/Pimple), который поставляется вместе с WPEmmerge. Он не такой мощный, как используемый в Brocooly [PHP DI контейнер](https://php-di.org/), однако более "управляемый". Это значит, что все зависимости Вы должны будете указать сами, никакого автосвязывания и уайлд-кард для группы классов и интерфейсов

Вы можете переписать вызов контроллера так, как это указано [здесь](https://docs.wpemerge.com/#/framework/routing/controllers?id=instantiation), то есть, если у Вас есть `CustomController`

```php
// CustomController.php

private $posts;

public function __construct( PostsRepositoryInterface $posts )
{
    $this->posts = $posts;
}
```

и репозиторий

```php
class PostsRepository implements PostsRepositoryInterface
{
    //...
}
```

Вы можете передать этот репозиторий в контейнер с сервисным слоем, где интерфейс репозитория будет являться ключом и передать его в параметры контроллера

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

Теперь переменная `$posts` внутри контроллера будет определена