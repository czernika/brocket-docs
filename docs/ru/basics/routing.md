# Routing

See [WPEmerge routing documentation](https://docs.wpemerge.com/#/framework/routing/request-lifecycle)

Brocket uses WPEMerge routing as a core but with some minor changes

First of all you may use `Route` facade instead of `Brocooly::route()` method but it is all up to you

```php
use Brocooly\Support\Facades\Route;

Route::get()->where( 'is_front_page' )->handle( /* callback */ );
```

Second one - see in this example `is_front_page` condition? Brocket has its own conditional methods based on this

```php
Route::is_front_page()->handle( /* callback */ );
```

As you can see WordPress conditional tag now used as a facade method. But not every conditional has its own method - list of allowed types is listed bellow

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