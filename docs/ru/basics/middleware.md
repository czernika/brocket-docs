# Middleware

See [WPEmerge middleware documentation](https://docs.wpemerge.com/#/framework/routing/middleware)

## Create Middleware

To create controller class run in your terminal

```sh
php brocooly new:middleware MiddlewareName
```

This will create `MiddlewareName.php` file within `Theme/Http/Middleware` directory

!> Don't forget to register middleware within `config/wpemerge.php` file as it was mentioned in WPEmerge documentation

Wright any logic within handle method