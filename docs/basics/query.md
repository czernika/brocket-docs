# Query

WordPress has `WP_Query`, Timber has `get_posts()` to retrieve all data but what does Brocket has? Well, both. And a little more.

Heavily inspired by Laravel Brocket queries are linked to its models - so no need to has `post_type` arguments within your queries.

You may call any query model related to your model like this

```php
CustomPostType::all();
```

This will get all posts which status is published and post_type is `CustomPostType::POST_TYPE`. There are a lot more methods - specific to Post Types, Taxonomies and common.

!> This list is not full - there are a lot more working examples in a development mode (or within Brocooly package which eventually will be relocated here) and will be released few releases after

## Common methods

May be applied as for Post Types and Taxonomies

### all()

Get all post types. When we say "all" we do not really mean all - `posts_per_page` query limit is set within `query.php` configuration file as `defaults`

```php
// Get all posts
Post::all();
```

### get()

As methods may be chained this particular method ends QueryBuilder and retrieves posts by provided query.

### query( array $query )

Set custom query if you're stack. Basically it is just arguments for `WP_Query`.

```php
// Get all posts where page id is 6
Post::query( [
    'p' => 6
] )->get();
```

### paginate( ?int $postsPerPage = null )

Paginate all posts. If not set default query param will be used

```php
// Get 3 posts per page
Post::paginate( 3 )->get();
```

### where( string $key, $value )

Where clause. Set any custom key and its value into query

```php
// Get all posts where page id is 6
Post::where( 'p', 6 )->get();
```

This method may be chained further

```php
// Get all posts where page id is 6 AND author id is 1
Post::where( 'p', 6 )
    ->where( 'author', 1 )
    ->get();
```

### collect()

Retrieves [\Illuminate\Support\Collection](https://laravel.com/docs/8.x/collections) instead of posts. It is up to you what would you like to do with it

### first()

Get first object of collection

```php
Post::first();

// Get first post where author id is 1
Post::where( 'author', 1 )
    ->get();
```

### last()

Get last object of collection

```php
Post::last();

// Get last post where author id is 1
Post::where( 'author', 1 )
    ->get();
```

### withStatus( string|array $status )

Get posts with provided post statuses

```php
// Get all posts with draft status
Post::withStatus( 'draft' )->get();
```

### withDrafts()

Get posts with `draft` post status

```php
// Get all posts with `draft` and `publish` status
Post::withDrafts()->get();
```

### withTrashed()

Get posts with `trash` post status

```php
// Get all posts with `trash` and `publish` status
Post::withTrashed()->get();
```

## Post Types

Post Type specific queries

### id( int $id )

Get post by id

```php
Post::id( 1 );
```

### current()

Get current post object

```php
Post::current();
```

## Taxonomies

Taxonomy specific queries

!> Note for Taxonomy queries default `post_type` param will be set as post_types linked to its taxonomy

### term( string $field, $terms, string $operator = 'IN' )

Sets `tax_query`. 

```php
// Get all categories terms which id is 6
Category::term( 'id', 6 )->get();
```

### termId( $terms, string $operator = 'IN' )

Shortcut for `term()` method

```php
// Get all categories terms which id is 6
Category::termId( 6 )->get();
```

### termName( $terms, string $operator = 'IN' )

Shortcut for `term()` method

```php
// Get all categories terms which `name` is 'Bob'
Category::termName( 'Bob' )->get();
```

### termSlug( $terms, string $operator = 'IN' )

Shortcut for `term()` method

```php
// Get all categories terms which `slug` is 'bob'
Category::termSlug( 'bob' )->get();
```

### relation( string $relation = 'AND' )

Sets tax query relation

```php
// Get all categories terms which `slug` is 'bob' or 'dave'
Category::termSlug( 'bob' )
        ->relation( 'OR' )
        ->termSlug( 'dave' )
        ->get();
```

### or()

Sets tax query relation as 'OR'. Shortcut for `relation()` method

```php
// Get all categories terms which `slug` is 'bob' or 'dave'
Category::termSlug( 'bob' )
        ->or()
        ->termSlug( 'dave' )
        ->get();
```

### and()

Sets tax query relation as 'AND'. Shortcut for `relation()` method

> Note - order doesn't matter

```php
// Get all categories terms which `slug` is 'bob' and 'dave'
Category::termSlug( 'bob' )
        ->termSlug( 'dave' )
        ->and()
        ->get();
```

### taxQuery( array $query )

Set your own tax query. Used for quite complicated queries

```php
// Get all categories terms which `slug` is 'bob' and 'dave'
Category::taxQuery( /* args for `tax_query` */ )
        ->get();
```

### terms( $args = null, array $maybe = [] )

Get list of all categories terms

```php
// Get list of all categories terms
Category::terms();
```