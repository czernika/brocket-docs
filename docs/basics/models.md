# Models

Brocket Model is a realization of Timber's `Post` and `Term` classes. Basically WordPress has few basic models, two of them are post type and taxonomy. These conventions are serves as core concept for models under hood of Brocket

## Post Types

To register custom post type (or create custom post type model speaking in Brocket-way) run the following command

```sh
php brocooly new:model:post_type PostType
```

Within generated model it is crucial to have at least two options:

- First of all, it need to use `RequiresRegistrationTrait` to be able to registered via WordPress core.

```php
use Brocooly\Models\PostType;
use Brocooly\Support\Traits\RequiresRegistrationTrait;

class CustomPostType extends PostType
{
    use RequiresRegistrationTrait;
}
```

- Second condition - you need to fill at least `args()` method which should return array of arguments for post type registration. Basically it is same list as for [register_post_type()](https://developer.wordpress.org/reference/functions/register_post_type/) but as Brocket uses Extended post types package, you may also check [register_extended_post_type()](https://github.com/johnbillion/extended-cpts/wiki/Registering-Post-Types) function arguments. Other params (for example, for [WPGraphQL](https://www.wpgraphql.com/)) should be registered here.

```php
use Brocooly\Models\PostType;
use Brocooly\Support\Traits\RequiresRegistrationTrait;

class CustomPostType extends PostType
{
    use RequiresRegistrationTrait;

    public function args()
    {
        return [
            // arguments
        ];
    }
}
```

To help you more, there are two additional methods - `labels()` (which should return array of labels for your post types. It will be included as $args['labels'] within post types arguments if present) and `names()` - it has plural, singular form of post type label and slug (see [here](https://github.com/johnbillion/extended-cpts/wiki))

- And finally you must have `POST_TYPE` constant. It is same as a first argument of `register_post_type()` function - basically it is slug (if not overwritten) so it should be unique among all post types

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
            // arguments
        ];
    }
}
```

## Taxonomy

Basically same concept is correct for custom taxonomies with a few differences

```sh
php brocooly new:model:taxonomy Taxonomy --post_type PostType
```

`--post_type` flag is optional - if it is not present Taxonomy will be linked to `Post` model (which is a `post`). This flag should point the namespace of model with `Theme\Models` prefix. So if your post type has `Theme\Models\Custom` namespace the option should be `--post_type Custom/PostType`. If no such class present console will show warning but Taxonomy class will be created. Post types will defined within `$postTypes` property.

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
            // arguments
        ];
    }
}
```

Taxonomy is not same as PostType and registered via [register_extended_taxonomy()](https://github.com/johnbillion/extended-cpts/wiki/Registering-taxonomies) function.

## Registration

To register custom post types and taxonomies you should pass class name into `config/models.php` file within `post_types` and `taxonomies` keys respectively

!> Dont' forget flush permalinks immediately after - go to your Settings - Permalinks and hit Save changes button or type `wp rewrite flush` command

## Relations

You may specify relations for your models. Currently there is only one but it is kinda basic relation - post type belongs to specific taxonomy

Example below will show you relation between `Book` post type and `Genre` taxonomy

```php
class Book extends PostType
{
    public function genres()
    {
        return $this->belongsToTaxonomy( Genre::TAXONOMY );
    }
}
```

Within your twig templates you may access terms simply as

```twig
{{ book.genres }}
```

This will return an array of genre terms for specific book.

## Metaboxes

To add metaboxes to custom post types and taxonomies (and not only - you may metaboxes for posts the same way) you need to use `HasPostMetaboxes` or `HasTermMetaboxes` traits within respective models.

This wil provide some methods for you but you have to create one at least called `metaboxes()`. This one should void some action which will be hooked into Carbon Fields [carbon_fields_register_fields](https://docs.carbonfields.net/quickstart.html) hook - all Carbon fields options are available but there are few methods to help you with

!> Every post type and taxonomy which has metaboxes needs to be registered within `models.php` configuration file.

> You may pass `-m` flag when creating your model to have quick access to its methods

### setContainer( string $id, string $title )

This method helps you to quickly register container without defining where clause and will return `Carbon_Fields\Container` object - so you may continue to use Carbon Fields conditions and methods further. Just pass an unique container id and title (for end users) and start adding metaboxes to it

### setContainerWithFields( string $id, string $title, array $fields )

Same as above but with a third argument - an array of metaboxes

### Meta Facade

To help you register metaboxes quicker Brocket provides `Brocooly\Support\Facades\Meta` Facade which has almost every `Carbon_Fields\Field` [types](https://docs.carbonfields.net/quickstart.html) (except for sidebar)

So these lines do the same

```php
Field::make( 'text', 'crb_text', 'Text Field' );

Meta::text( 'crb_text', 'Text Field' );
```

## User

**In development**

## Comment

**In development**