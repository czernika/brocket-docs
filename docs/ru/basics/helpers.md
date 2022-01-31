# Helper Functions

Brocket provides some helper functions along with Laravel's Illuminate Collection helper functions

## dd( $var )

Dump and die helper

## dump( $var )

Debug helper. Output debug information but does **NOT** stops site work

## isCurrentEnv( string $env )

Checks is current environment is `$env` or not

Returns `bool`

## isProduction()

Checks if is current environment is `production`

Returns `bool`

## config( ?string $key = null )

Get configuration file key. Key must be in a dotted notation, where first key word - is config file name.

For example `config( 'app.some.key' )` will return `value` from `'some' => [ 'key' => 'value' ]` key within `config/app.php` file

Returns `mixed`

## app( ?string $key = null )

Get app container key or app itself

Returns `mixed`

## output( string|array $view, array $ctx = [] )

Output helper for Controllers. Renders twig template file with context `$ctx`

## asset( string $filePath )

Get full asset path by its key within manifest file

Returns `string`
