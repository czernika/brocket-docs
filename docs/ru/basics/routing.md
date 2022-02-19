# Маршрутизация :id=routing

WordPress самостоятельно обрабатывает входящие запросы и для обработки таких запросов предоставляет свое решение - так называемую [иерархию шаблонов](https://developer.wordpress.org/themes/basics/template-hierarchy/). WPEmerge же при запуске основного класса приложения `Theme\Brocooly` цепляется к хуку `template_include` и обрабатывает эти запросы внутри себя. Конечной точкой в итоге все же остается файл `index.php` в корне темы, который лучше всего использовать для вывода непредвиденных ошибок или же оставить пустым

?> Чтобы узнать больше о маршрутизации, прочтите [документацию WPEmerge](https://docs.wpemerge.com/#/framework/routing/request-lifecycle)

!> Даже если Вы его не используете, файл `index.php` в корне темы ни в коем случае нельзя удалять - иначе WordPress будет считать Вашу тему поломанной и не увидит в списке доступных тем

## Основы маршрутизации :id=basics

Файлы маршрутов находятся в корне темы внутри директории `routes` и подключаются внутри файла конфигурации `wpemerge.php`

> Всего по-дефолту поддерживаются [три](https://docs.wpemerge.com/#/framework/routing/defining-routes?id=defining-routes) именованные группы маршрутов (они же посредники) - `web`, `admin` и `ajax`

Документация WPEmerge содержит подробное описание создания базовых [маршрутов](https://docs.wpemerge.com/#/framework/routing/request-lifecycle) по УРЛ и особенностях маршрутизации для WordPress, так что здесь мы остановимся лишь на различиях c Brocket и принципиальных моментах

Во-первых, Вы можете использовать фасад `Brocooly\Support\Facades\Route` вместо вызова метода `Brocooly::route()` напрямую

```php
use Theme\Brocooly;
use Brocooly\Support\Facades\Route;

Brocooly::route()->get()->where( 'is_front_page' )->handle( /* коллбэк-функция */ );

// или
Route::get()->where( 'is_front_page' )->handle( /* коллбэк-функция */ );
```

Оба примера выше идентичны, однако во-втором случае синтаксис становится короче

## Условные теги :id=conditional

Во-вторых, Brocket расширяет возможности WPEmerge и добавляет условные теги WordPress в маршрутизацию

> Например, можно использовать условие `is_single` вместо того чтобы писать маршруты для постов и не вызывать конфликт с ядром WordPress и его sitemap. Особенно это сложно с учетом того, что и посты, и страницы имеют одинаковую структуру УРЛ, а вот условный тег у них разный. Во-вторых, условные теги учитывают различную структуру пермалинков

Brocket использует их, чтобы упростить `GET`-маршрутизацию - Вы можете писать имя функции как условие, включая те же самые параметры. Таким образом, выражение выше можно переписать еще проще

```php
Route::is_front_page()->handle( /* коллбэк-функция */ );

Route::is_page( 1 )->handle( /* коллбэк-функция сработает на странице с id=1 */ );

Route::is_post_type_archive( 'news' )->handle( /* сработает на архиве кастомного пост тайпа `news` */ );
```

> Не все условные теги могут использоваться, как имя метода - некоторые попросту бесполезны с точки зрения маршрутизации. Список "разрешенных" тегов со ссылкой на страницу с описанием их размещен ниже

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
- [is_page_template()](https://developer.wordpress.org/reference/functions/is_page_template/)
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

## Простой маршрут для представлений :id=output

Если создаваемая страница не нуждается в обработке большого кол-ва информации и представляет из себя, например, простую верстку, Вы можете использовать метод `output( array|string $views, array $ctx = [] )`, который по сути своей является оболочкой для вспомогательной функции `output()` и принимает точно такие же аргументы - файл представления и его локальный контекст

```php
// Вернет файл представлений content/front.twig на главной странице
Route::is_front_page()->output( 'content.front', [ 'foo' => 'bar' ] );
```

> Метод `output()` является дополнительным замыкающим методом - он не может стоять посередине цепочки, а лишь завершать (замыкать) ее. Остальные три - `handle()`, `view()` и `group()`.

## 404-я страница и другие статусы :id=statuses

Важным моментом является следующее - при нахождении точки вхождения (то есть, удовлетворению условия), Brocket будет возвращать ответ, указанный внутри коллбэк-функции - а по умолчанию это 200 - даже на странице 404, хотя условие `is_404()` и будет возвращать истину. Происходит это потому, что ответ для `is_404()` возвращает глобальный объект `WP_Query`, а вот статус страницы - чаще всего объект `Psr\Http\Message\ResponseInterface`. Чтобы привести все к единому знаменателю, ответу внутри функции-обработчика нужно присвоить статус при помощи метода `withStatus( $status )`. Например, для страницы 404 это будет выглядеть так:

```php
Route::is_404()->handle( 'NotFoundController@index' );

class NotFoundController
{
    public function index()
    {
        return output( 'path.to.404.view' )->withStatus( 404 );
    }
}
```

> Список всех возможных методов обработки ответа сервера можно найти [здесь](https://docs.wpemerge.com/#/framework/routing/controllers?id=response-objects) 

Второй способ позволить решить эту проблему - не писать никакого вхождениям для `is_404` для маршрутов. В таком случае, запрос в конечном итоге "уткнется" в файл `index.php` и сам WordPress будет решать судьбу статуса. Однако уже внутри самого файла Вы должны будете обработать запрос (проще говоря, сверстать страницу) вне контроллера

Тема Brocooly по умолчанию предоставляет два маршрута - для главной страницы и как раз для любой страницы с любым статусом, не удовлетворяющим ни одному условию. Обработка такого запроса идет внутри метода `all` контроллера `Theme\Http\Controller\PageController`

```php
public function all()
{
    $responseCode = http_response_code();

    $error = [
        'status' => $responseCode,
        'text'   => match ( $responseCode ) {
            200     => __( 'Please provide appropriate template for this request', 'brocooly' ),
            404     => __( 'Page was not found', 'brocooly' ),
            500     => __( 'Server error', 'brocooly' ),
            default => __( 'There has been a critical error on this website', 'brocooly' ),
        },
    ];
    return output( 'content.error', compact( 'error' ) )->withStatus( $responseCode );
}
```

 Обрабатываются три произвольных статуса - 200 (в случае, когда запрос удовлетворяет какому-либо условию, но Вы забыли предоставить для него правильное условие или шаблон), 404 (страница не найдена) и статусы критических ошибок сервера. Вы можете дополнить этот метод другими статусами (например, 403 или 503) или же полностью переписать маршруты по своему вкусу 