# Маршрутизация :id=routing

## Основы маршрутизации с WPEmerge :id=basics

Чтобы узнать больше о маршрутизации, прочтите [документацию WPEmerge о маршрутах](https://docs.wpemerge.com/#/framework/routing/request-lifecycle)

## Условные теги :id=conditional

Brocket использует маршрутизацию WPEMerge в основе, но с двумя дополнениями

Во-первых, Вы можете использовать фасад `Brocooly\Support\Facades\Route` вместо метода `Brocooly::route()`

```php
use Brocooly\Support\Facades\Route;

Route::get()->where( 'is_front_page' )->handle( /* коллбэк-функция */ );

// то же самое, что и
Brocooly::route()->get()->where( 'is_front_page' )->handle( /* коллбэк-функция */ );
```

Второе - обратили ли Вы внимание на условие `is_front_page` в примере? Это так называемый условный тег WordPress, который объявляет, что текущая страница является главной. Brocket использует эти условия, чтобы упростить `get`-маршрутизацию - Вы можете писать имя метода как условие.

```php
Route::is_front_page()->handle( /* коллбэк-функция */ );
```

Но не все условные теги могут использоваться, как имя метода (некоторые из них требуют указание параметров, некоторые попросту бесполезны с точки зрения маршрутизации) - так что список разрешенных тегов со ссылкой размещен ниже

- [is_404()](https://developer.wordpress.org/reference/functions/is_404/)
- [is_archive()](https://developer.wordpress.org/reference/functions/is_archive/)
- [is_attachment()](https://developer.wordpress.org/reference/functions/is_attachment/)
- [is_author()](https://developer.wordpress.org/reference/functions/is_author/)
- [is_category()](https://developer.wordpress.org/reference/functions/is_category/)
- [is_date()](https://developer.wordpress.org/reference/functions/is_date/)
- [is_day()](https://developer.wordpress.org/reference/functions/is_day/)
- [is_front_page()](https://developer.wordpress.org/reference/functions/is_front_page/)
- [is_home()](https://developer.wordpress.org/reference/functions/is_home/)
- [is_month()](https://developer.wordpress.org/reference/functions/is_month/)
- [is_page()](https://developer.wordpress.org/reference/functions/is_page/)
- [is_paged()](https://developer.wordpress.org/reference/functions/is_paged/)
- [is_post_type_archive()](https://developer.wordpress.org/reference/functions/is_post_type_archive/)
- [is_privacy_policy()](https://developer.wordpress.org/reference/functions/is_privacy_policy/)
- [is_search()](https://developer.wordpress.org/reference/functions/is_search/)
- [is_single()](https://developer.wordpress.org/reference/functions/is_single/)
- [is_singular()](https://developer.wordpress.org/reference/functions/is_singular/)
- [is_sticky()](https://developer.wordpress.org/reference/functions/is_sticky/)
- [is_tag()](https://developer.wordpress.org/reference/functions/is_tag/)
- [is_tax()](https://developer.wordpress.org/reference/functions/is_tax/)
- [is_time()](https://developer.wordpress.org/reference/functions/is_time/)
- [is_year()](https://developer.wordpress.org/reference/functions/is_year/)