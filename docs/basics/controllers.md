# Контроллеры :id=controllers

Вместо того, чтобы обрабатывать входящий запрос в файлах маршрутов, можно выносить логику в специальные классы-контроллеры

## Создание контроллера :id=creating-controller

Чтобы создать такой класс, запустите команду в терминале

```sh
php brocooly new:controller <ControllerName>
```

`ControllerName` может содержать в себе пространство имен контроллера относительно `Theme\\Http\\Controllers\\`, то есть, `PostsController` преобразуется в `Theme\\Http\\Controllers\\PostsController`, `Posts/Controller` в `Theme\\Http\\Controllers\\Posts\\Controller` и так далее

### Параметры :id=console-params

Команда может принимать следующие дополнительные флаги

`--constructor, -c` - для создания метода `__construct()` дополнительно в контроллере
`--invokable, -i` - для создания метода `__invoke()` вместо `index` (по-умолчанию)

Флаги можно совмещать, так команда

```sh
php brocooly new:controller <ControllerName> -ci
```

создаст как `__construct()`, так и `__invoke()` методы

## Посредники :id=middleware

В контроллере Вы можете указать посредника. Он будет применяться ко всем методам контроллера, если не сказано иное, например

```php
public function __construct()
{
    $this->middleware('middleware');
}
```

!> Метод применяет в себя только строку (один посредник), в отличие от посредников маршрута, где можно указать сразу массив посредников

Если Вам нужно, чтобы посредник применялся к определенному методу, укажите методы или их список в методе `only`

```php
public function __construct()
{
    $this->middleware('middleware')->only('index');
}
```

Посредник `middleware` будет применяться только к методу `index`. Или наоборот

```php
public function __construct()
{
    $this->middleware('middleware')->except('index', 'single');
}
```

В этом случае посредник будет применяться ко всем методам, кроме `index` и `single`

!> При совмещении посредников маршрута и контроллера первыми будут выполняться посредники маршрута

## Контроллер одного действия :id=invokable

Если Ваш контроллер выполняет всего одно действие, можно создать метод `__invoke` и перенести логику в него. В маршруте тогда можно не указывать имя метода, а лишь передать имя класса (вместе с пространством)

```php
use Theme\Http\Controllers\SomeController;

Route::get('/hello', SomeController::class); // без указания метода
```

## Ответ :id=response

Контроллер (как и любой обработчик маршрута) должен возвращать либо строку, либо объект `Brocooly\Http\Responses\ResponseInterface`

Подробнее можно найти [здесь](/basics/routing.md?id=response)

## Валидация входящего запроса

**@TODO** Создать объект валидатора, параметры, локализацию
