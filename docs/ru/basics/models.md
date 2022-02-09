# Модели :id=models

Brocket реализует свои модели с использованием классов `Timber\Post` и `Timber\Term`. Хотя нельзя полноценно назвать эти классы моделями, однако они используют схожие методики для работы с базой данных через объект `Timber\PostQuery`

## Пост тайпы :id=post-types

Чтобы зарегистрировать кастомный пост-тайп (или создать модель, говоря языком Brocket), выполните следующую команду

```sh
php brocooly new:model:post_type CustomPostType
```

Внутри сгенерированного класса нужно выполнить два условия:

1. Вы должны использовать `Brocooly\Support\Traits\RequiresRegistrationTrait`, чтобы иметь возможность регистрации пост тайпа в ядре WordPress. Без него регистрация будет проигнорирована, так как существуют уже ранее зарегистрированные модели ('post', 'page') или же модели, зарегистрированные при помощи плагинов

```php
use Brocooly\Models\PostType;
use Brocooly\Support\Traits\RequiresRegistrationTrait;

class CustomPostType extends PostType
{
    use RequiresRegistrationTrait;
}
```

2. Модель должна содержать метод `args()`, который должен возвращать массив с опциями для регистрации пост-тайпа. По сути, это те же самые параметры, что и для родной функции [register_post_type()](https://developer.wordpress.org/reference/functions/register_post_type/), однако Вы можете использовать аргументы функции [register_extended_post_type()](https://github.com/johnbillion/extended-cpts/wiki/Registering-Post-Types), так как Brocket использует именно ее. Другие параметры (например, для [WPGraphQL](https://www.wpgraphql.com/)) также должны быть зарегистрированы внутри этого метода

> В случае, если модель уже существует, этот шаг можно опустить

```php
use Brocooly\Models\PostType;
use Brocooly\Support\Traits\RequiresRegistrationTrait;

class CustomPostType extends PostType
{
    use RequiresRegistrationTrait;

    public function args()
    {
        return [
            // аргументы
        ];
    }
}
```

При генерации класса Brocket также создает два вспомогательных метода - `labels()` (возвращает массив имен для пост тайпа. Он будет включен как `$args['labels']` внутри аргументов пост тайпа, если сам метод присутствует) и `names()` - для указания одиночной и множественной форм ярлыков пост тайпа и его слаг (подробнее [здесь](https://github.com/johnbillion/extended-cpts/wiki))

3. Наконец, самое главное Вы должны определить константу `POST_TYPE`. Она должна соответствовать первому аргументу функции `register_post_type()` function - по сути, это слаг пост тайпа, так что он должен быть уникальным среди всех зарегистрированных пост тайпов

```php
use Brocooly\Models\PostType;
use Brocooly\Support\Traits\RequiresRegistrationTrait;

class CustomPostType extends PostType
{
    use RequiresRegistrationTrait;

    public const POST_TYPE = 'custom_post_type';

    public function args()
    {
        return [
            // аргументы
        ];
    }
```

## Таксономии :id=taxonomies

По сути, точной такой же концепт, как и для пост тайпов, однако с некоторыми различиями

Во-первых, таксономия принадлежит пост тайпу и привязывается к нему, потому можно указать пост тайп при создании таксономии

```sh
php brocooly new:model:taxonomy CustomTaxonomy --post_type CustomPostType
```

Опция `--post_type` **НЕ** является обязательной - если его нет, генерируемая таксономия будет привязана к модели `Post`. Значение опции должно совпадать с пространством имен модели, за вычетом префикса `Theme\Models`. Если Ваша модель расположена под именем `Theme\Models\Custom`, значение опции должно быть `--post_type Custom/PostType`. Если модели пост тайпа не существует, консоль укажет предупреждение об этом, однако таксономия все равно будет создана. Значение пост тайпа должно быть определено внутри переменной `$postTypes`

```php
use Theme\Models\WP\Post;
use Brocooly\Models\Taxonomy;
use Brocooly\Support\Traits\RequiresRegistrationTrait;

class CustomTaxonomy extends Taxonomy
{
    use RequiresRegistrationTrait;

    public const TAXONOMY = 'custom_taxonomy';

    protected string|array $postTypes = [ Post::POST_TYPE ];

    public function args()
    {
        return [
            // аргументы
        ];
    }
}
```

Аргументы для таксономий должны совпадать с аргументами для функции [register_extended_taxonomy()](https://github.com/johnbillion/extended-cpts/wiki/Registering-taxonomies)

## Регистрация :id=registration

To register custom post types and taxonomies you should pass class name into `config/models.php` file within `post_types` and `taxonomies` keys respectively

!> Dont' forget flush permalinks immediately after - go to your Settings - Permalinks and hit Save changes button or type `wp rewrite flush` command

## Отношения :id=relations

Вы можете указать взаимосвязь Ваших пост тайпов и таксономий внутри модели для вывода их на фронте сайта. В настоящий момент у Brocket имеется только одна, хотя и самая основная связь - пост тайп принадлежит таксономии

Например, у Вас есть пост тайп `Book` с таксономией `Genre`. Тогда взаимосвязь между двумя этими моделями можно определить таким образом

```php
class Book extends PostType
{
    public function genres()
    {
        return $this->belongsToTaxonomy( Genre::TAXONOMY );
    }
}
```

Внутри шаблонов twig Вы можете получить массив жанров просто выполнив (при условии, что переменная `book` определена в контексте)

```twig
{{ book.genres }}
```

Альтернативой данному решению выступает [встроенный](https://timber.github.io/docs/reference/timber-post/#terms) в `Timber\Post` метод `terms()`, внутри которого можно указать таксономию

```php
class Book extends PostType
{
    public function genres()
    {
        return $this->terms( Genre::TAXONOMY );
    }
}
```

Однако данный метод будет создавать запрос к базе каждый раз, когда встречается в цикле - так что для архивных страниц это может показаться плохой идеей. В то время как метод `belongsToTaxonomy()` создает запрос один раз и фильтрует значения уже внутри себя. Минусом же такого подхода является то, что Вы получаете все термы таксономии вне зависимости от того, используются они или нет внутри конкретного объекта пост тайпа. Так что здесь нужно держать баланс и думать, что для Вас выгоднее с точки зрения оптимизации. Для малого количества термов и внутри цикла рекомендуется использовать отношение, на одиночных страницах и при большом количестве термов - метод `terms()`

## Метабоксы :id=metaboxes

Метабкосы представляют из себя дополнительные блоки опций для определенной модели, доступные локально (только для определенного объекта, в отличие от опций темы). Функционал метабоксов поставляется из-под коробки WordPress, однако Brocket использует решение CarbonFields для быстрого и удобного создания разнообразных метабоксов, включая сложные репитеры 

Чтобы создать метабоксы для пост тайпов и таксономий, вы должны использовать трейты `HasPostMetaboxes` или `HasTermMetaboxes` внутри соответствующих моделей

Чтобы создать метабоксы, внутри Вашей модели должны присутствовать метод `metaboxes()`. Он должен будет выполнить любое действие по регистрации метабоксов, которое будет подключаться в хук Carbon Fields [carbon_fields_register_fields](https://docs.carbonfields.net/quickstart.html). Любые методы Carbon Fields будут доступны для регистрации метабоксов

!> Любая модель должна быть зарегистрирована внутри файла конфигурации `models.php` - даже зарегистрированные через ядро WordPress или плагины (например, `Post`)

> При создании класса модели через консоль Вы можете указать флаг `-m`, чтобы создать методы для регистрации метабоксов внутри модели

Brocket предоставляет несколько вспомогательных методов для регистрации контейнера для метабоксов и самих полей

### setContainer( string $id, string $title )

Этот метод позволяет быстро зарегистрировать контейнер для текущей модели и будет возвращать объект `Carbon_Fields\Container` так что можно будет продолжать регистрацию полей через методы CarbonFields. Все, что Вам нужно сделать, это лишь предоставить уникальный id контейнера и его заголовок

```php
public function metaboxes()
{
    return $this->setContainer( 'brocket_container_id', __( 'Brocket Container', 'brocooly' ) )
        ->add_field( //... );
}
```

### setContainerWithFields( string $id, string $title, array $fields )

Метод делает то же самое, что и `setContainer()`, но третьим параметром принимает массив метабоксов

```php
public function metaboxes()
{
    return $this->setContainerWithFields(
        'brocket_container_id',
        __( 'Brocket Container', 'brocooly' ),
        [
            // поля
        ],
    );
}
```

### Фасад Meta

Чтобы ускорить регистрацию метабоксов, Brocket предоставляет фасад `Brocooly\Support\Facades\Meta`, который поддерживает практически все [поля](https://docs.carbonfields.net/quickstart.html) `Carbon_Fields\Field`, за исключением сайдбаров

Методы фасада принимают два параметра - айди и заголовок метабокса, в то время как именем метода является тип метабокса - самое первое значение метода `Field::make()`

Таким образом, следующие две строки кода выполняют одно и то же действие

```php
Field::make( 'text', 'crb_text', __( 'Text Field', 'brocooly' ) );

Meta::text( 'crb_text', __( 'Text Field', 'brocooly' ) );
```

## User :id=user

**В разработке**

## Comment :id=comment

**В разработке**