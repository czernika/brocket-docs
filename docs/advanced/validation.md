# Валидация :id=validation

Для того, чтобы запрос можно было проверить на соответствие правилам, он должен наследоваться от `Brocooly\Http\Requests\FormRequest`. Тогда будет доступен метод `validator()`, который получит объект валидатора `Illuminate\Validation\Validator`

```php
Route::post('/hello', function (FormRequest $request) {
    dd($request->validator());
});
```

В метода валидатора можно передать правила, сами данные для проверки, произвольные сообщения об ошибках и аттрибуты. Если этого не сделать, класс будет искать эти данные внутри себя из соответствующих методов `rules(), messages() и customAttributes()` соответственно. Данные возьмутся из параметра запроса, а именно файлы, параметры и аттрибуты.

```php
Route::post('/hello', function (FormRequest $request) {
    dd($request->validator([
        'param1' => 'some_value',
        'param2' => [
            'another_value_1',
            'another_value_2',
        ]
    ], [
        'param1' => 'list|of|rules',
        'param2' => [
            'list', 'of', 'rules', new CustomRuleForArray(),
        ],
        'param2.*' => 'rules|for|array|element'
    ]));
});
```

Чтобы проверить файлы формы, у самой формы должен быть аттрибут `enctype="multipart/form-data"`

Метод `validate()` валидатора бросает `Illuminate\Validation\ValidationException`, так что этот блок следует оборачивать в `try-catch`

Чтобы вернуться назад с ошибками, Вы можете использовать следующую конструкцию

```php
Route::post('/hello', function (FormRequest $request) {
	try {
        $request->validator(rules: [
            'email' => 'required|string|email',
        ])->validate();
    } catch (ValidationException $e) {
        return redirect()->withErrors($e->errors())->back();
    }
	
	return redirect()->back();
});
```

Переменная `errors` будет доступна на фронте в виде массива, где ключ - поле провалившегося инпута, а значение - массив связанных с ним ошибок. Сессия истечет, как только Вы перезагрузите страницу

## Методы валидатора :id=methods

Такие же, как у `Illuminate\Validation\Validator`

@TODO Дать ссылку, расписать, добавить пару примеров

## Создание произвольного правила :id=custom-rule



## Ответы валидатора, локализация :id=messages