# Query

WordPress has `WP_Query`, Timber has `get_posts()` to retrieve all data but what does Brocket has? Well, both. And a little more.

Heavily inspired by Laravel Brocket queries are linked to its models - so no need to has `post_type` arguments within your queries.

You may call any query model related to your model like this

```php
CustomPostType::all();
```

This will get all posts which status is published and post_type is `CustomPostType::POST_TYPE`. There are a lot more methods - specific to Post Types, Taxonomies and common.

!> This list is not full - there are a lot more working examples in a development mode (or within Brocooly package which eventually will be relocated here) and will be released few releases after

## Base query

### all()

Get all post types. When we say "all" we do not really mean all - `posts_per_page` query limit is set within `query.php` configuration file as `limit` option

```php
// If query.limit set to 300 will return 300 posts. 
Post::all();
```

> To disable query limit and get really all posts set its value to `-1`

### get()

This is the most common method to use as it a query builder finisher which will apply all query params was set before. It will return not QueryBuilder but the result itself. In every case it should finish the chain of queries 

### query( array $query )

Set custom query `$query` if you're stack with default methods. Basically it is just set arguments for `WP_Query` merged with defaults

```php
// Get all posts where author id is 1 in a descending order
Post::query(
    [
        'author' => 1,
        'order'  => 'DESC',
    ],
)->get();
```

Note the `get()` method at the end.

!> You do not need to pass `post_type` param as it is already set by builder and depends on model

### with( array $postsIn )

Give an extra array of posts which will be included into query

```php
// Posts with id 1 and 3 will be included in result
Post::query( /* query */ )->with( [ 1,3 ] )->get();
```

### except( array $postsIn )

Give an extra array of posts which **NOT** will be included into query

```php
// Posts with id 1 and 3 will NOT be included in result
Post::query( /* query */ )->except( [ 1,3 ] )->get();
```

### exceptCurrent()

Remove current post type in a result. Useful for showing related posts

```php
Post::query( /* query */ )->exceptCurrent()->get();
```

## Pagination Query

### paginate( ?int $postsPerPage = null )

Paginate all posts. If not set default query param will be used

```php
// Get 3 posts per page
Post::paginate( 3 )->get();
```

!> If you see 404 error on a custom post type pages like `/page/2`, you may use `pre_get_posts` hook. See [here](https://timber.github.io/docs/guides/pagination/#pagination-with-pre_get_posts)  

### offset( int $offset )

Set query offset

```php
// Get all posts after first three in a query
Post::offset( 3 )->get();
```

### paged( int $paged )

Set paged query parameter. Most of the time it doesn't required as Timber get this param on himself, but if any error ocurred feel free to use it 

```php
// Get all posts after first three in a query
Post::offset( 3 )->get();
```

## Sort Query

### sort( string $order, string $orderby )

Set `order` and `orderby` keys of query respectively

```php
// Get all posts in a descending order by title
Post::sort( 'DESC', 'title' )->get();

// Order by menu_order and next by title
Post::sort( 'ASC', 'menu_order title' )->get();
```

### asc( string $orderby = 'ID' )

Sort posts by `$orderby` key in a ascending order. Default is post ID

```php
// Order by menu_order and next by title
Post::asc( 'menu_order title' )->get();
```

### desc( string $orderby = 'ID' )

Sort posts by `$orderby` key in a ascending order. Default is post ID

```php
// Order by title
Post::desc( 'title' )->get();
```

### orderBy( array $sortQuery )

Set custom `orderby` key

```php
// Set custom order query - descending by title and ascending by menu order
Post::orderby( [ 'title' => 'DESC', 'menu_order' => 'ASC' ] )->get();
```

## Conditional Query

### where( string $key, $value )

Set any query param and its value you wish

```php
// Get all posts where author id is 1
Post::where( 'author', 1 )->get();
```

This method may be chained as much as you need

```php
Post::where( 'author', 1 )
    ->where( 'post_status', 'draft' )
    ->where( 'order', 'DESC' )
    ->where( 'foo', 'bar' )
    ->get();
```

By the way all methods may be chained within. So the query above is same as this

```php
Post::where( 'author', 1 )
    ->where( 'post_status', 'draft' )
    ->where( 'foo', 'bar' )
    ->desc() // get all posts in descending order by ID
    ->get();
```

### when( $condition, $callback )

Accept condition `$condition` (boolean value or valid callable) and callback `$callback` function which fires if condition is `true` - otherwise it will be ignored. The callback accept Query builder as a parameter so you may define query method within this method.

Basically this

```php
$postsQuery = Post::query( /* query */ );

if ( is_front_page() ) {
    $postsQuery->paginate( 1 );
}

$posts = $postsQuery->get(); // $posts will contain all posts or only one on a front page.
```

may be replaced with

```php
$posts = Post::query( /* query */ )
    ->when( 'is_front_page', fn( $q ) => $q->paginate( 1 ) )
    ->get();
```

## Meta Query

### meta( array $query )

Set meta query argument into `meta_query` array

```php
Post::meta(
    [
        'key'   => 'color',
	    'value' => 'blue',
    ],
)->get();
```

will be result in a next `WP_Query` arguments

```php
[
    'meta_query' => [
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
    ],
]
```

You may chain this method and it will set a new meta query argument

```php
Post::meta(
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
    )
    ->meta(
        [
            'key'   => 'age',
            'value' => '18',
        ],
    )
    ->get();

// -->
[
    'meta_query' => [
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
        [
            'key'   => 'age',
            'value' => '18',
        ],
    ],
]
```

### metaQuery( array $metaQuery )

Set whole meta query array for complex queries

```php
Post::metaQuery(
    [
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
        [
            'key'   => 'age',
            'value' => '18',
        ],
    ],
)->get();
```

### whereMeta( string $key, $value )

Sets `meta_key` and `meta_value` params in a query.

```php
// Get all posts where 'color' meta has value of 'blue'
Post::whereMeta( 'color', 'blue' )->get();
```

### whereMetaKey( string $key )

Sets only `meta_key` parameter

```php
// Get all posts with 'color' meta no matter the value is
Post::whereMetaKey( 'color' )->get();
```

### whereMetaValue( $value )

Sets only `meta_value` parameter

```php
// Get all posts where any meta has value of 'blue'
Post::whereMetaValue( 'blue' )->get();
```

### metaRelation( string $relation = 'AND' )

Sets the `meta_query` relation
```php
Post::meta(
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
    )
    ->meta(
        [
            'key'   => 'age',
            'value' => '18',
        ],
    )
    ->metaRelation( 'OR' )
    ->get();

// -->
[
    'meta_query' => [
        'relation' => 'OR',
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
        [
            'key'   => 'age',
            'value' => '18',
        ],
    ],
]
```

### andMeta()

Sets meta relation as `AND`

```php
Post::meta(
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
    )
    ->meta(
        [
            'key'   => 'age',
            'value' => '18',
        ],
    )
    ->andMeta()
    ->get();
```

### orMeta()

Sets meta relation as `OR`

```php
Post::meta(
        [
            'key'   => 'color',
            'value' => 'blue',
        ],
    )
    ->meta(
        [
            'key'   => 'age',
            'value' => '18',
        ],
    )
    ->orMeta()
    ->get();
```

## Terms Query

!> Every Term query should be related to `Taxonomy` model not `PostType`. The PostType model returned by this methods is `Timber\Post` model. To override this behavior you need to set `returnAs()` method which will get a post type class name 

### term( string $field, $terms, string $operator = 'IN' )

Set `tax_query` key

```php
// Get all posts where category term id is equal 1
Category::term( 'id', 1 )->get();
```

### termId( $terms, string $operator = 'IN' )

Set tax query by id

```php
// Get all posts where category term id is equal 1
Category::termId( 1 )->get();
```

### termName( $terms, string $operator = 'IN' )

Set tax query by name

```php
// Get all posts where category term name is equal 'Brocket'
Category::termName( 'Brocket' )->get();
```

### termSlug( $terms, string $operator = 'IN' )

Set tax query by slug

```php
// Get all posts where category term slug is equal 'brocket'
Category::termSlug( 'brocket' )->get();
```

### taxRelation( string $relation = 'AND' )

Set tax query relation

```php
// Get all posts where category term slug is equal 'brocket' or 'brocooly'
Category::termSlug( 'brocket' )
    ->termSlug( 'brocooly' )
    ->taxRelation( 'OR' )
    ->get();
```

### andTax()

Set tax query relation as `AND`

```php
// Get all posts where category term slug is equal 'brocket' and 'brocooly'
Category::termSlug( 'brocket' )
    ->termSlug( 'brocooly' )
    ->andTax()
    ->get();
```

### orTax()

Set tax query relation as `AND`

```php
// Get all posts where category term slug is equal 'brocket' or 'brocooly'
Category::termSlug( 'brocket' )
    ->termSlug( 'brocooly' )
    ->orTax()
    ->get();
```

### taxQuery( array $query )

Set custom `tax_query`

```php
Category::taxQuery(
    [
		'relation' => 'AND',
		[
			'taxonomy' => 'genre',
			'field'    => 'slug',
			'terms'    => [ 'action', 'comedy' ],
		],
		[
			'taxonomy' => 'actor',
			'field'    => 'id',
			'terms'    => [ 103, 115, 206 ],
			'operator' => 'NOT IN',
		],
	],
)
    ->get();
```

### terms( $args = null, array $maybe = [] )

Get all taxonomy terms

```php
$terms = Category::terms();
```

### returnAs( string $classMap )

Override `Timber\Post` returned model if needed

```php
// instead of `Timber\Post` return array of `Post` objects
Category::term( 'id', 1 )->returnAs( Post::class )->get()
```

## Post Status Query

### whereStatus( string|array $status )

Get posts with given post status

```php
// Get all posts with `draft` post status 
Post::whereStatus( 'draft' )->get();
```

### withStatus( string|array $status )

This function allows you to add additional status to already existed

```php
// Get all posts with `draft` AND `publish` post status
Post::withStatus( 'draft' )->get();
```

### withDrafts()

Get posts with both `draft` AND `publish` post status

```php
// Get all posts with `draft` AND `publish` post status
Post::withDrafts()->get();
```

### withTrashed()

Get posts with both `trash` AND `publish` post status

```php
// Get all posts with `trash` AND `publish` post status
Post::withTrashed()->get();
```

### withFuture()

Get posts with both `future` AND `publish` post status

```php
// Get all posts with `future` AND `publish` post status
Post::withFuture()->get();
```

### drafts()

Get posts with `draft` post status

```php
// Get all posts with `draft` post status
Post::drafts()->get();
```

### trashed()

Get posts with `trash` post status

```php
// Get all posts with `trash` post status
Post::trashed()->get();
```

### future()

Get posts with `future` post status

```php
// Get all posts with `future` post status
Post::future()->get();
```

## AuthorQuery

### whereAuthor( $author )

Get posts with given author name, id or even many authors

```php
// Get posts where author name is 'brocket'
Post::whereAuthor( 'brocket' )->get();

// Get posts where author id is 1 or 2
Post::whereAuthor( [ 1, 2 ] )->get();
```

### whereAuthorId( int $id )

Get posts by author id

```php
// Get posts where author id is 1
Post::whereAuthorId( 1 )->get();
```

### whereAuthorName( string $name )

Get posts by author name

```php
// Get posts where author name is 'brocket'
Post::whereAuthorName( 'brocket' )->get();
```

### whereAuthors( array $authors )

Get posts by some of the authors

```php
// Get posts where author id is 1 or 2
Post::whereAuthors( [ 1, 2 ] )->get();
```

### exceptAuthors( array $authors )

Get posts by any author except given

```php
// Get posts where author id is not equal 1 or 2
Post::exceptAuthors( [ 1, 2 ] )->get();
```

## Date Query

### after( $date )

Get all posts after given date

```php
// Get all posts after 1st of January, 2021
Post::after( 'January 1st, 2021' )->get();
```

The date must in a string format or any readable by query

### before( $date )

Get all posts before given date

```php
// Get all posts before 1st of January, 2021
Post::before( 'January 1st, 2021' )->get();
```

### between( $after, $before )

Get all posts between two dates

```php
use Carbon\Carbon;

// Get all posts between 1st of January, 2021 and today
Post::between( 'January 1st, 2021', Carbon::today() )->get();
```

### whereDate( $dateQuery )

Set any date query you wish

```php
// Get all posts which were published 1 year ago but modified last month
Post::whereDate(
    [
        [
            'column' => 'post_date_gmt',
            'before' => '1 year ago',
        ],
        [
            'column' => 'post_modified_gmt',
            'after'  => '1 month ago',
        ],
    ],
)->get();
```

## Search Query

### search( string $key, bool $exact = false, bool $sentence = false )

Set search query by keyword `$key`

```php
// Get all posts which contains 'some' keyword
Post::search( 'some' )->get();
```

## Collection Query

Collection method works with `\Illuminate\Support\Collection` object

### collect()

Get posts collection

```php
Post::collect(); // returns Posts collection
```

Brocket support any available method for [Collections](https://laravel.com/docs/8.x/collections)

```php
// Get first post from query
Post::first();

// Last one
Post::last();

// Random
Post::random();

// Get only post title and id as a key (useful for select options)
Post::pluck( 'post_title', 'id' )->all();

// Get only first three posts
Post::take( 3 )->all();
```

and so on