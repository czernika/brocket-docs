# Маршрутизация :id=routing

Типичная маршрутизация WordPress построена на условных тегах и соответствующих им шаблонах - например, условию `is_archive()` соответствуют шаблоны `archive.php`, `index.php` согласно [иерархии шаблонов](https://developer.wordpress.org/themes/basics/template-hierarchy/). Такая система удобна, однако требует создания страниц в админке, пусть и пустых, или же дополнительной работе с пермалинками для создания нединамичных страниц, а также наличия определенного шаблона под каждый возможный запрос

Brocooly собирает все маршруты в едином файле и сортирует их согласно выполняемой логике - это маршрут по условному тегу или же УРЛ - и возвращает результат выполнения замыкания (проще говоря, ответ)

Все начинается в файле `index.php` в корне темы, где мы видим всего одну строку кода

```php
do_action('brocooly.index.response', app(GLOBAL_REQUEST_KEY));
```

Именно этот хук отвечает за вывод ответа на экран. Принимает он в себя один обязательный аргумент - это объект `Brocooly\Requests\RequestInterface` и именно на его основе возвращается ответ сервера, будет ли маршрут определен или нет

!> Ни в коем случае не удаляйте этот файл - это требование WordPress для нахождения темы. Он модет быть пустым, однако в таком случае WordPress будет выполнять стандартную логику маршрутизации

Определения маршрутов находятся в корне директории `routes`, однако Вы можете изменить (или добавить) файлы маршрутов внутри метода `routes` провайдера `RouterServiceProvider`. Ключ (например, `web`) - это имя посредника, который вызывается при вызове каждого маршрута

?> По сути, создается группа маршрутов с единым посредником для всех - подробнее об этом [здесь](/basics/routing?id=group)

```php
class RouteServiceProvider extends AbstractServiceProvider
{
	public function boot()
	{
		$this->routes([
			'web' => [
				'path' => routes_path() . '/web.php', // путь к файлу маршрутов
			],
		]);
	}
}
```

!> Помните - в файлах маршрутов глобальная переменная `$wp_query` еще не определена

## УРЛ-маршрутизация (по регулярному выражению) :id=url-routing

Маршрутизатор использует вспомогательный фасад `Brocooly\Support\Facades\Route` для создания маршрутов и позволяет регистрировать любые HTTP-методы в качестве маршрутов

```php
use Brocooly\Support\Facades\Route;

Route::get($uri, $callback); // GET-метод
Route::post($uri, $callback); // POST
Route::put($uri, $callback); // PUT
Route::patch($uri, $callback); // PATCH
Route::delete($uri, $callback); // DELETE
Route::options($uri, $callback); // OPTIONS
```

Таким образом, при регистрации данного маршрута

```php
Route::get('/hello', fn() => 'Hello World');
```

если Вы пройдете в браузере по адресу `http://example.com/hello`, Вы увидите надпись `Hello World`

Альтернативным способом регистрации метода является следующая запись

```php
Route::methods('GET', '/hello', fn() => 'Hello World');
```

Она вернет точно такой же результат, что и метод выше. Разница лишь в том, что `methods` может принимать в себя сразу несколько методов, если такая необходимость возникает

```php
Route::methods(['GET', 'POST'], '/hello', fn() => 'Hello World');

// или
Route::methods('GET|POST', '/hello', fn() => 'Hello World');
```

Таким образом, и GET, и POST-запросы адресу `/hello` будут возвращать 'Hello World' в качестве ответа

Хорошо, а что насчет динамического контента, например, записей? Скажем, мы создали две записи, адрес которых `/posts/hello-world` и `/posts/hello-brocooly`. Можно создавать маршрут для каждой, однако для динамически созданного контента мы не можем знать, что введет клиент и какие параметры валидны. Здесь может регулярное выражение

## Регулярные выражения :id=regex-routing

Вместо строки мы можем ввести доступное регулярное выражение, например

```php
Route::get('/posts/:string', fn() => 'Hello World');
```

где `:string` - любое выражение, которое соответствует регулярному выражению `([\w\-_]+)`.

Соответственно, на страницах `/posts/hello-world` и `/posts/hello-brocooly` мы увидим ответ 'Hello World'.

Список доступных регулярных выражений:

```php
protected array $patterns = [
    ':all' => '(.*)',
    ':any' => '([^/]+)',
    ':id' => '(\d+)',
    ':int' => '(\d+)',
    ':number' => '([+-]?([0-9]*[.])?[0-9]+)',
    ':float' => '([+-]?([0-9]*[.])?[0-9]+)',
    ':bool' => '(true|false|1|0)',
    ':string' => '([\w\-_]+)',
    ':slug' => '([\w\-_]+)',
    ':uuid' => '([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})',
    ':date' => '([0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[1-2][0-9]|3[0-1]))',
];
```

Некоторые выражения повторяются для удобства (например, string-slug, int-id, number-float).

Для выражения `:all` есть короткая запись, которая принимает в себя только обработчик

```php
Route::any(fn() => 'Hello World');
```

Таким образом, для любых страниц будут выводиться 'Hello World'. Это может быть полезно для обработки "неожиданных" страниц или ошибок

В примере чуть выше мы указали, что обе страницы вернут один и тот же ответ. Это совершенно не то, что мы обычно хотим видеть. Потому настало время поговорить об обработчиках запросов

### Произвольный запрос :id=custom-regex

Если Вам необходимо создать произвольное регулярное выражение для маршрута, используйте метод `where()`

```php
Route::get('/posts/:something', fn() => 'Hello World')
        ->where([':something' => '(.*)']);
```

Можно передать любое регулярное выражение и любой ключ. При совпадении наименования ключей будет использоваться произвольное выражение

```php
Route::get('/posts/:id', fn() => 'Hello World')
        ->where([':id' => '(.*)']);

Route::get('/posts/:id', fn() => 'Hello World')
        ->where([':id' => 5]);
```

Несмотря на то, что ключ `:id` используется для валидации только цифр, мы указали, что он соответствует любому вхождению и таким образом валидным будет только путь с id = 5

Если параметр может быть, а может и не быть, выражение следует обрамить вопрос с двух сторон, и указать дефолтное значение параметра

```php
Route::get('/posts/?:id?', fn($request, $id = 0) => $id);
```

На странице `/posts` мы увидим 0, а на остальных - переданный параметр `:id`

## Обработчики :id=handlers

В качестве обработчика можно указать несколько типов данных

### Анонимная функция  :id=anonymous-handler

Такая функция должна возвращать `ResponseInterface` или строку

```php
Route::get('/hello', fn() => 'Hello World');

// или
Route::get('/hello', fn() => view('index.twig'));
```

> Во втором случае будет отображен контента файла `index.twig`

### Метод контроллера :id=controller-handler

Можно создать контроллер, который будет наследоваться от `Brocooly\Http\Controllers\AbstractController` и вызывать его метод. Сделать это можно тремя способами

#### Строковый вызов :id=controller-string
```php
Route::get('/hello', 'SomeController@index');

// или
Route::get('/hello', 'SomeController::index');
```

В данном случае будет вызван метод `index` контроллера `SomeController`. Пространство имен для данного контроллера можно указать в методе `routes` провайдера `RouteServiceProvider` (по-умолчанию это `Theme\\Http\\Controllers\\`)

#### Вызов callable :id=controller-callable

```php
use Theme\Http\Controllers\SomeController;

Route::get('/hello', [SomeController::class, 'index']);
```

!> Изменить пространство имен можно также при помощи метода `namespace` - см. здесь

Делает то же самое, что и выше, однако здесь больше контроля над пространством имен и этот метод гораздо ближе к нативному PHP

#### Контроллер одного вызова :id=controller-invokable

Если контроллер использует всего один метод, вместо его создания можно указать метод `__invoke`, и регистрация маршрута будет выглядеть следующим образом

```php
use Theme\Http\Controllers\SomeController;

Route::get('/hello', SomeController::class); // без указания метода
```

```php
class SomeController extends AbstractController
{
	public function __invoke()
	{
		return view('index.twig');
	}
}
```

Абсолютно каждый обработчик принимает в качестве первого параметра объект `RequestInterface`, который можно будет модифицировать в контроллере или получить от него необходимые данные

```php
Route::get('/hello', [SomeController::class, 'index']);
```

```php
use Brocooly\Http\Requests\RequestInterface;

class SomeController extends AbstractController
{
	public function index(RequestInterface $request)
	{
		return view('index.twig');
	}
}
```

Когда используется маршрутизация по регулярному выражению, помимо объекта запроса, параметрами выступают и все найденные вхождения, то есть

```php
Route::get('/posts/:id/hello/:slug', [SomeController::class, 'index']);
```

Для маршрута `/posts/17/hello/world` мы получим

```php
use Brocooly\Http\Requests\RequestInterface;

class SomeController extends AbstractController
{
	public function index(RequestInterface $request, $id, $slug)
	{
        dump($id); // '17'
        dump($slug); // 'world'
		return view('index.twig');
	}
}
```

!> Обратите внимание - возвращаемый тип `$id` - `string` (не `integer`)

> В отличие от Laravel, где параметры функции передаются через сервисный контейнер, здесь обратная ситуация - контейнер передает параметры методу

## Типы ответа :id=response

Возвращать функция обработчик должна либо строку, либо объект `Brocooly\Http\Responses\ResponseInterface`. Во втором случае к ответу можно применить дополнительные методы для модификации ответа, например, установить статус маршрута

### view()

Метод возвращает скомпилированный twig файл с переданным контекстом

```php
Route::get('/hello', fn() => view('index.twig'));
```

Скомпилирует файл `index.twig`. Функция принимает те же параметры, что и `Timber::compile()`.

### json()

Возвращает json ответ, используется для API или AJAX. Параметром служит объект для сериализации

```php
Route::post('/hello', fn() => json(['foo' => 'bar']));
```

### redirect()

Устанавливает редирект на указанный адрес

```php
Route::post('/hello', fn() => redirect('/to'));

// или
Route::post('/hello', fn() => redirect()->to('/'));
```

Чтобы вернуться назад, можно использовать метод `back()` (полезно для обработчика форм)

```php
Route::post('/hello', fn() => redirect()->back());
```

### output()

Обрабатывает переданную строку в ответ

```php
Route::get('/hello', fn() => output('Hello World'));
```

### response()

Создает пустой объект ответа, который можно модифицировать самостоятельно

```php
Route::get('/hello', fn() => response()->withStatus(500));
```

## Файлы представлений :id=view

Иногда для простых файлов представлений, которым не требуется обработка и передача данных, проще указать маршрут, который возвращает указанный файл

```php
Route::view('/hello', 'index.twig');
```

Вместо замыкания мы указываем имя передаваемого файла. Также можно указать массив файлов, первый, который найдется, будет использован - если файл `example.twig` не существует, будет грузиться `index.twig`

```php
Route::view('/:any', ['example.twig', 'index.twig']);
```

Можно передать дополнительные данные в контекст

```php
Route::view('/hello', 'index.twig', ['foo' => 'bar']);
```

Подробнее о контексте читайте здесь

!> @TODO Создать страницу контекста, добавить ссылку

## Группы маршрутов :id=group

Можно создать группу маршрутов, чтобы установить им одинаковые свойства
Первым параметром может быть "общий" префикс пути или массив любых аргументов маршрута

```php
Route::group('/admin', function () {
    Route::get('/', /** замыкание */); // '/admin'
    Route::get('/posts', /** замыкание */); // '/admin/posts'
});
```

Список параметров, которые могут быть установлены в массиве:

1. 'prefix' - для указания общего префикса группы
2. 'namespace' - пространство имен контроллера
3. 'name' - префикс имени всех маршрутов
4. 'middleware' - посредник или группа посредников, применяемая к маршрутам

```php
Route::group([
	'prefix' => '/posts',
	'namespace' => 'Theme\\Custom\\',
	'name' => 'posts.',
    'middleware' => 'custom_middleware'
], function () {
	Route::get('/', 'Controller@index')
            ->name('index'); // posts.index
	Route::get('/:id', 'Controller@example')
            ->name('show'); // posts.show
});
```

!> Значения `prefix` и `name` могут быть установлены только для УРЛ-маршрутов, они будут игнорироваться для WordPress маршрутов 

## Редиректы :id=redirect

Вы можете использовать метод для перенаправления с одного УРЛ на другой. Третьим аргументом идет статус, по умолчанию равен 302 (HTTP_MOVED)

```php
Route::redirect('/from', '/to');
Route::redirect('/from', '/to', 301);
```

Для постоянного перенаправления можно использовать метод `permanentRedirect` со статусом 301

```php
Route::permanentRedirect('/from', '/to');
```

## Условные теги WordPress :id=conditional-routing

Теперь, когда разобрались с маршрутизация по УРЛ, вернемся к началу. Все-таки мы работаем с WordPress и терять удобные теги `is_archive()` и прочее не хочется

И не надо - Brocooly предоставляет условные маршруты с теми же параметрами, что и функции WordPress, например

```php
Route::is_front_page(/** обработчик */); // сработает на главной странице
Route::is_archive(/** обработчик */); // сработает на странице архива
Route::is_archive('genre', /** обработчик */); // сработает на странице архива таксономии 'genre'
Route::is_singular(/** обработчик */); // сработает на странице любого пост тайпа
Route::is_singular('book', /** обработчик */); // сработает на странице пост тайпа 'book'
Route::is_page([1,2], /** обработчик */); // сработает на страницах с id 1 или 2
```

Brocooly не меняет входящий запрос WordPress - так что если вы удалите хук `brocooly.index.response` и создадите файл `single.php`, то входящий запрос для записей будет попадать именно в этот файл

?> Если нужно добавить условия для каких плагинов (например, `is_shop`), Вы можете передать строку, которая соответствует имени функции, в массив `$extraConditionals` класса ядра `Theme\Http\Kernel`

```php
/**
	 * Array of WordPress conditionals
	 *
	 * Add new keys like `is_shop`
	 *
	 * @var string[]
	 */
	protected array $extraConditionals = [
		'is_shop',
	];
```

Теперь можно создавать маршрут

```php
Route::is_shop(/** параметры */);
```

!> Все создаваемые таким методом маршруты могут быть **ТОЛЬКО** GET-методами

## AJAX-маршрутизация :id=ajax

Маршрутизация происходит при помощи `wp_ajax_` хуков. Для создания маршрутов AJAX можно использовать метод `ajax()`

```php
Route::ajax('action_name', /** замыкание */, 'POST', true);
```

где `POST` - метод маршрута, а `true` устанавливает возможность запуска запроса неавторизованным пользователям

В директории `routes` можно заметить два файла - `web.php` и `ajax.php`. Метод `ajax` может быть помещен в любой из них. Так в чем же смысл?

А смысл в том, что при помещении обычного УРЛ-маршрута внутри файла `ajax.php` автоматически превращает его в AJAX-маршрут

Так, внутри файла `ajax.php` пример выше может быть переписан как

```php
Route::post('action_name', /** замыкание */)->isPublic();
```

!> AJAX-маршруты НЕ поддерживают создание произвольных имен - оно формируется автоматически из префикса `ajax_` и имени экшена

## Параметры :id=parameters

К каждому маршруту или группе при создании можно задать дополнительные параметры

### middleware(string|array $middleware) :id=middleware

Указать посредник (или посредники) для данного маршрута

```php
Route::get('/hello', 'Controller@index')->middleware('middleware1');
Route::get('/hello', 'Controller@index')->middleware(['middleware1', 'middleware2']);
```

### namespace(string $namespace) :id=namespace

Указать пространство имен для маршрута

```php
Route::get('/hello', 'Controller@index')->namespace('Theme\\Custom\\');
```

> В таком случе файл контроллера должен находиться по пути src\Custom\Controller.php

### name(string $name) :id=name

Для произвольных маршрутов можно установить имя маршрута

```php
Route::get('/hello', 'Controller@index')->name('hello');
```

Методы можно ставить в цепочку

```php
Route::get('/hello-world', 'Controller@index')
    ->namespace('Theme\\Custom\\')
    ->name('hello');
```

Чтобы получить УРЛ именованного маршрута, можно воспользоваться функцией `route()` - он принимает имя маршрута первым параметром

```php
dd(route('hello')); // '/hello-world'
```

Вторым параметром можно указать параметры УРЛ (строкой, если один, или массивом, если их несколько), например

```php
Route::get('/posts/:id', 'Controller@index')
    ->name('posts.show');

Route::get('/posts/:slug/comments/:id', 'Controller@index')
    ->name('comments');

dd(route('posts.show', 34)); // '/posts/34'
dd(route('comments', ['hello-world', 15])); // '/posts/hello-world/comments/15'
```

Для AJAX маршрутов передавать нужно имя экшена с префиксом `ajax_`

```php
Route::post('custom', 'Controller@index');

dd(route('ajax_custom')); // '/wp/wp-admin/admin-ajax.php?action=custom'
```