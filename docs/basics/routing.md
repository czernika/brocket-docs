# Маршрутизация

Типичная маршрутизация WordPress построена на условных тегах и соответствующих им шаблонах - например, условию `is_archive()` соответствуют шаблоны `archive.php`, `index.php` согласно иерархии шаблонов. Такая система удобна, однако требует создания страниц в админке, пусть и пустых, или же дополнительной работе с пермалинками для создания нединамичных страниц, а также наличия определенного под каждый возможный запрос

Brocooly собирает все маршруты в едином файле и сортирует их согласно выполняемой логике - это маршрут по условному тегу или же УРЛ - и возвращает результат выполнения замыкания (проще говоря, ответ)

Все начинается в файле `index.php` в корне темы

```php
do_action('brocooly.index.response', app(GLOBAL_REQUEST_KEY));
```

Именно этот хук отвечает за вывод ответа на экран. Принимает он в себя один обязательный аргумент - это объект `RequestInterface` и именно на его основе возвращается ответ

!> Ни в коем случае не удаляйте этот файл - это требование WordPress для нахождения темы

Определения маршрутов находятся в корне директории `routes`, однако Вы можете изменить (или добавить) файл маршрутов внутри `routes` метода провайдера `RouterServiceProvider`. Ключ `web` - это имя посредника, который вызывается при вызове каждого маршрута

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

Подробнее о посредниках [здесь](/basics/midlleware.md)

## УРЛ-маршрутизация (по регулярному выражению)

Маршрутизатор использует вспомогательный фасад `Brocooly\Support\Facades\Route` для создания маршрутов и позволяет регистрировать любые HTTP-методы в качестве маршрутов

```php
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

## Регулярные выражения

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

Для выражения `:all` есть короткая запись, которая принимает в себя только обработчик

```php
Route::any(fn() => 'Hello World');
```

В примере выше мы указали, что обе страницы вернут один и тот же ответ. Это совершенно не то, что мы обычно хотим видеть. Потому настало время поговорить об обработчиках запросов

## Обработчики

В качестве обработчика можно указать несколько типов данных

### Анонимная функция

Такая функция должна возвращать `ResponseInterface` или строку

```php
Route::get('/hello', fn() => 'Hello World');

// или
Route::get('/hello', fn() => view('index.twig'));
```

> Во втором случае будет отображен контента файла `index.twig`

### Метод контроллера

Можно создать контроллер, который будет наследоваться от `Brocooly\Http\Controllers\AbstractController` и вызывать его метод. Сделать это можно тремя способами

#### Строковый вызов
```php
Route::get('/hello', 'SomeController@index');

// или
Route::get('/hello', 'SomeController::index');
```

В данном случае будет вызван метод `index` контроллера `SomeController`. Пространство имен для данного контроллера можно указать в методе `routes` провайдера `RouteServiceProvider` (по-умолчанию это `Theme\\Http\\Controllers\\`)

#### Вызов callable

```php
use Theme\Http\Controllers\SomeController;

Route::get('/hello', [SomeController::class, 'index']);
```

!> Изменить пространство имен можно также при помощи метода `namespace` - см. здесь

Делает то же самое, что и выше, однако здесь больше контроля над пространством имен и этот метод гораздо ближе к нативному PHP

#### Контроллер одного вызова

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

Абсолютна каждый обработчик принимает в качестве первого параметра объект `RequestInterface`, который можно будет модифицировать в контроллере или получить от него необходимые данные

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

## Файлы представлений

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

!> Помните - в файлах маршрутов глобальная переменная `$wp_query` еще не определена

### Группы маршрутов

Можно создать группу маршрутов, чтобы установить им одинаковые свойства
Первым параметром может быть "общий" префикс пути или массив любых аргументов маршрута

```php
Route::group('/admin', function () {
    Route::get('/', /** замыкание */); // '/admin'
    Route::get('/posts', /** замыкание */); // '/admin/posts'
});

Route::group(['namespace' => 'Custom\\', 'prefix' => '/admin'], function () {
    Route::get('/', /** замыкание */); // '/admin'
    Route::get('/posts', /** замыкание */); // '/admin/posts'
});
```

### Условные теги WordPress

Теперь, когда разобрались с маршрутизация по УРЛ, вернемся к началу. Все-таки мы работаем с WordPress и терять удобные теги `is_archive()` и прочее не хочется

И не надо - Brocooly предоставляет условные маршруты с теми же параметрами, что и функции WordPress, например

```php
Route::is_front_page(/** обработчик */); // сработает на главной странице
Route::is_archive(/** обработчик */); // сработает на странице архива
Route::is_archive('genre', /** обработчик */); // сработает на странице архива таксономии 'genre'
Route::is_singular('book', /** обработчик */); // сработает на странице пост тайпа 'book'
Route::is_page([1,2], /** обработчик */); // сработает на страницах с id 1 или 2
```

Brocooly не меняет входящий запрос WordPress - так что если вы удалите хук `brocooly.index.response` и создадите файл `single.php`, то входящий запрос для записей будет попадать именно в этот файл

## Параметры

К каждому маршруту или группе при создании можно задать дополнительные параметры

### middleware(string|array $middleware)

Указать посредник (или посредники) для данного маршрута

```php
Route::get('/hello', 'Controller@index')->middleware('middleware1');
Route::get('/hello', 'Controller@index')->middleware(['middleware1', 'middleware2']);
```

### namespace(string $namespace)

Указать пространство имен для маршрута

```php
Route::get('/hello', 'Controller@index')->namespace('Theme\\Custom\\');
```

> В таком случе файл контроллера должен находиться по пути src\Custom\Controller.php

### name(string $name)

Для произвольных маршрутов можно установить имя маршрута

```php
Route::get('/hello', 'Controller@index')->name('hello');
```

Методы можно ставить в цепочку

```php
Route::get('/hello', 'Controller@index')
    ->namespace('Theme\\Custom\\')
    ->name('hello');
```