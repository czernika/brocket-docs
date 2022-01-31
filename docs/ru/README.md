# О фреймворке :id=about

[![GitHub license](https://img.shields.io/github/license/czernika/brocket)](https://github.com/czernika/brocket/blob/master/LICENSE) [![GitHub release](https://img.shields.io/github/v/release/czernika/brocket)](https://gitHub.com/czernika/brocket/releases/) ![Last commit](https://img.shields.io/github/last-commit/czernika/brocket)

Фреймворк для разработчиков WordPress, вдохновленный [фреймворком Laravel](https://laravel.com/), и основанный на решениях [Timber](https://timber.github.io/docs/guides/wp-integration/) и [Wpemerge](https://wpemerge.com/) для разработки тем со структурой [Bedrock](https://roots.io/bedrock/)

## Почему Brocket? :id=why-brocket

Хотя WordPress в своем ядре поставляет практически все, что нужно, работать с ним немного неудобно. Все эти функции, глобальные переменные, различный синтаксис... 

Вдохновленный Laravel, Brocket Framework был создан, чтобы привнести элегантность и простоту Laravel в ядро WordPress, чтобы создавать мощные сайты и приложения со структурой MVC, объектно-ориентированном подходом и удобным шаблонизатором twig в качестве файлов представлений. Фреймворк не ставит в основе своей цели переписать WordPress, а лишь дополнить его и улучшить работу с ним для разработчиков. Ничего более

### ООП и MVC :id=oop

Посмотрите сами - скажем, что нам нужно получить все посты из базы данных. Как мы жто сделаем? Достаточно просто

```php
// WordPress
$args = [
    'post_type'      => 'post',
    'posts_per_page' => -1,
];

$posts = new WP_Query( $args );
```

Неплохо. Но как аналогичный запрос выглядит у Brocket?

```php
// Brocket
$posts = Post::all();
```

Ларавельно!

Brocket предоставляет множество вспомогательных методов для запросов (не только `all()`) и для **ЛЮБОЙ** модели пост тайпа и таксономии

### Представления :id=views

В основе представлений лежит шаблонизатор twig, входящий вместе с пакетом Timber. Однако, использование нативных php шаблонов не возброняется. **В теории**, Вы даже можете подключить шаблонизатор blade, так как WPEmerge предоставляет такую возможность

!> Однако использование шаблонизатора blade не было протестировано от слова "совсем". Во-первых, ядро фреймворка все равно будет тянуть twig, как зависимость Timber. Во-вторых, я подозреваю несовместимость версий PHP. Однако при большом желании - пожалуйста, вот ссылка на [WPEmerge blade](https://github.com/htmlburger/wpemerge-blade) пакет

Brocket обрабатывает маршрутизацию при помощи WPEmerge - так что прочтите их [документацию](https://docs.wpemerge.com/#/framework/routing/request-lifecycle) для понимания того, как это происходит. У Brocket есть фасад `Route` для маршрутизации, так что вместо `App::route()` Вы можете вызывать `Route` - как в предыдущих версиях WPEmerge. 

### Структура Bedrock :id=bedrock

Bedrock предоставляет улучшенную безопасность сайта и более удобную файловую структуру. Вы можете узнать более об [организации папок](https://roots.io/bedrock/) и почему Вы должны использовать ее

!> Так как Bedrock имеет другую структуру папок и произвольное расположение контента, могут возникать конфликты с некоторыми плагинами - особенно с теми, которые имеют тенденцию менять содержимое файла `wp-config.php`. Вы можете переписать их содержимое в файл конфигурации окружения и все должно быть в порядке. Но все же, даже с крупными "продуманными" плагинами могут возникать те или иные проблемы

### Расширенные возможности при создании пост тайпов и таксономий :id=ecpt

Brocket использует пакет [extended post types](https://github.com/johnbillion/extended-cpts), который значительно улучшает юзабилити администратора при работе с пост тайпами и таксономиями - простое создание фильтров, колонки в таблицах и иные простые, но полезные опции

### Простое создание метабоксов и опций темы :id=metaboxes-and-theme-mods

Создание метабоксов и настроек темы никогда не было столь простым вместе с [CarbonFields](https://carbonfields.net/) и [Kirki Framework](https://kirki.org/). Brocket использует оба пакета в основе своей и еще больше упрощает работу с ними!

## Благодарность :id=thanks

Brocket Framework своим существованием обязан следующим авторам и их пакетам:

- [WPEmerge](https://wpemerge.com/) - основа фреймворка. У WPEmerge отличная система маршрутизации и посредников, а также инициализации проекта. К тому же, при создании и регистрации метабоксов Brocket использует библиотеку [CarbonFields](https://carbonfields.net/);
- [Timber](https://timber.github.io/docs/guides/wp-integration/) - пакет Composer, который поменял мое отношение к WordPress. Приносит шаблонизатор [twig](https://twig.symfony.com/) в тему, а также упрощенные модели и обработчики запросов;
- [Bedrock](https://roots.io/bedrock/) - структура ядра и папок фреймворка. Улучшает безопасность сайта за счет иного шифрования паролей, иной структуры папок и "скрытого" нередактируемого файла `wp-config.php`;
- [Kirki Framework](https://kirki.org/) - простое и удобное создание многообразных опций темы, включая крайне полезный `repeater`;
- [Extended Post Types](https://github.com/johnbillion/extended-cpts) - улучшенное создание пост тайпов и таксономий. Отдельное спасибо за плагин [Query Monitor](https://querymonitor.com/)
- [Laravel](https://laravel.com/) - идейный вдохновитель и поставщик пакетов `illuminate` для фреймворка. Тейлор, ты гений!;
- [WordPress](https://wordpress.org/) - ядро, без которого фреймворка и не существовало бы;
- [TailwindCSS](https://tailwindcss.com/) - самый лучший CSS фреймворк.

И остальным авторам пакетов, зависимостей и плагинов, которые используется в основе фреймворка

## Лицензия :id=license

Brocket Framework использует открытую MIT [лицензию](https://github.com/czernika/brocket/blob/master/LICENSE.md)

![Brocket Framework](../_media/screenshot.png)