# Посредники :id=middleware

Посредники служат для преобразования входящего запроса и возврата ответа по указанной логике приложения

## Создание файла посредника :id=creating-middleware

Чтобы создать класс посредника, запустите команду в терминале

```sh
php brocooly new:middleware <MiddlewareName>
```

Сгенерированный файл будет находится под пространством имен `Theme\\Http\\Middleware`

## Применение посредника :id=handle

Посредник должен содержать метод `handle()`, принимающий минимум объект запроса и функцию замыкания. Возвращать посредник должен объект ответа или строку

```php
public function handle(RequestInterface $request, Closure $next)
{
    if (true) {
        // Вернуть какой-то ответ или преобразовать входящий запрос
    }

    return $next($request);
}
```

Посредник можно использовать в методах, указав имя класса или же зарегистрировать его "ключ" в классе `Theme\Http\Kernel`

```php
/**
 * Array of app middleware
 *
 * @var array<string, string>
 */
protected array $middleware = [
    'web' => WordPressDefined::class,
    'ajax' => IsDoingAjax::class,
];
```

Ключом может быть любое уникальное строковое значение. После регистрации в методы маршрута или контроллера можно передавать ключ вместо имени класса

```php
Route::get('/', 'Controller@index')->middleware('web'); // будет применен посредник WordPressDefined
```

> По-умолчанию к группе маршрутов `web.php` применяется посредник 'web', для `ajax.php` - 'ajax'.

## Посредники приложения :id=app-middleware

Если нужно защитить все маршруты приложения каким-то общим посредником, можно дополнительно указать его в массиве `$appliedMiddleware` класса ядра `Theme\Http\Kernel`
