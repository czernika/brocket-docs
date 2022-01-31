# Helper Functions :id=helpers

Brocket provides some helper functions along with Laravel's Illuminate Collection helper functions

## dd( $var ) :id=dd

Dump and die helper

## dump( $var ) :id=dump

Debug helper. Output debug information but does **NOT** stops site work

## isCurrentEnv( string $env ) :id=is-current-env

Checks is current environment is `$env` or not

Returns `bool`

## isProduction() :id=is-production

Checks if is current environment is `production`

Returns `bool`

## config( ?string $key = null ) :id=config

Get configuration file key. Key must be in a dotted notation, where first key word - is config file name.

For example `config( 'app.some.key' )` will return `value` from `'some' => [ 'key' => 'value' ]` key within `config/app.php` file

Returns `mixed`

## app( ?string $key = null ) :id=app

Get app container key or app itself

Returns `mixed`

## output( string|array $view, array $ctx = [] ) :id=output

Output helper for Controllers. Renders twig template file with context `$ctx`

## asset( string $filePath ) :id=asset

Get full asset path by its key within manifest file

Returns `string`
