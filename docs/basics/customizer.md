# Customizer :id=customizer



## Секции

Каждая секция должна иметь минимум два именованных метода - `args()` и `fields()`, а также иметь уникальный идентификатор `SECTION_ID`. Каждая секция должна быть зарегистрирована внутри файла конфигурации `config/customizer.php`

Чтобы создать секцию кастомайзера с ее настройками, введите следующую команду

```sh
php brocooly new:customizer:section <SectionName>
```

Созданный файл появится под именем `Theme\\Customizer\\Sections\\SectionName`

### Аргументы

Аргументы возвращают массив аргументов секции или же строку, если нужно вернуть только название секции

```php
protected function args() : string|array
{
    return [
        'title' => 'Section Title',
        'description' => 'Description',
        'priority' => 10,
    ];
}

// или
protected function args() : string|array
{
    return 'Section Title';
}
```

Во втором случае будет зарегистрирована секция с заголовком 'Section Title' и дефолтными настройками

### Настройки

Метод `fields()` должен возвращать массив настроек. Пути создания настроек два

1. Простая регистрация при помощи Kirki

Просто поместите в массив регистрацию настройки ровно так же, как в документации Kirki. Но помните, что в таком случае надо будет указывать все настройки опции, включая id секции

Например,

```php
protected function fields() : array
{
    return [
        new \Kirki\Field\Editor(
            [
                'settings'    => 'editor_setting',
                'label'       => esc_html__( 'My Editor Control', 'kirki' ),
                'description' => esc_html__( 'This is an editor control.', 'kirki' ),
                'section'     => 'section_id',
                'default'     => '',
            ]
        ),
    ];
}
```

2. При помощи фасада `Brocooly\Support\Facade\Mod`. Для примера выше это будет

```php
protected function fields() : array
{
    return [
        Mod::editor('editor_setting', [
            'label'       => esc_html__( 'My Editor Control', 'kirki' ),
            'description' => esc_html__( 'This is an editor control.', 'kirki' ),
            'default'     => '',
        ]),
    ];
}
```

> Обратите внимание - в первом случае все равно требуется указывать ID секции, во втором она подхватится автоматически

Можно также смешивать настройки

## Панели

Панели создаются для объединения секций - они **НЕ** содержат никаких настроек. Создаются панели при помощи команды

```sh
php brocooly new:customizer:panel <PanelName>
```

Созданный файл появится под именем `Theme\\Customizer\\Panels\\PanelName`

Чтобы приписать секцию к определенной панели, нужно в массиве ее аргументов указать айди панели

```php
protected function args() : string|array
{
    return [
        'title' => 'Section Title',
        'description' => 'Description',
        'priority' => 10,
        'panel' => PanelName::PANEL_ID,
    ];
}
```

При создании секции можно привязать ее к определенной панели

```sh
php brocooly new:customizer:section <SectionName> --panel=<PanelName>
```

## Отдельные настройки



## Опции сайта



## Вывод настроек

Чтобы вывести настройку на сайте, можно использовать метод темы

```php
protected function fields() : array
{
    return [
        Mod::text('example_setting', 'Example title'),
    ];
}
```

Представим, что в админке мы ввели слово 'Hello'

```twig
{{ theme.theme_mod('example_setting') }} // 'Hello'
```

Вторым параметром она принимает дефолтное значение, если настройки не существует

```twig
{{ theme.theme_mod('example_setting', 'Callback') }} // 'Callback', если настройки не существует
```