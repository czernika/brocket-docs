# Providers

See [WPEmerge providers documentation](https://docs.wpemerge.com/#/framework/tools/service-providers)

## Create Provider

To create service provider class run in your terminal

```sh
php brocooly new:provider ProviderName
```

This will create `ProviderName.php` file within `Theme/Providers` directory

To register provider pass its class name into `providers` key within `config/wpemerge.php` file.

!> Order matter! First provider will register features first and will be booted first

`ThemeServiceProvider` is used to register custom theme features, hooks and other options. Consider it like `functions.php`, but fired even before theme