# Customizer (Theme mods) :id=customizer

WordPress [customizer](https://codex.wordpress.org/Theme_Customization_API) allows you to create theme options which may be visually edited. As it is quite job to do Brocket uses the power of [Kirki Framework](https://kirki.org) under the hood. 

## Sections :id=sections

The base of customizer is its options but without section none will be created. So first things first - let's create custom theme section

Run in your terminal

```sh
php brocooly new:customizer:section SectionName
```

This will create basic customizer section panel with one simple setting within.

There are three vital settings:

- constant `SECTION_ID` - must be unique. All custom options created outside of this class must use this key as `section` argument
- method `args()` - section arguments like type, title, etc
- method `fields()` - section options (or fields) - must return an array of customizer options

Brocket uses `Mod` facade to help you with options but ypu may use native customizer settings by Kirki This mean if you want to create simple text option with label only these two lines of code do the same thing

```php
Mod::text( 'example_setting', esc_html__( 'Example setting', 'brocooly' ) )

new \Kirki\Field\Text(
	[
		'settings' => 'example_setting',
		'label'    => esc_html__( 'Example setting', 'brocooly' ),
		'section'  => 'some_section',
	]
);
```

> Note that with use of `Mod` facade you're may skip `section` argument, while within Kirki fields it is required

## Panels :id=panels

Panels basically same thing as section but it has no options and it is a wrapper for a group of sections

```sh
php brocooly new:customizer:panel PanelName
```

When creating a section ypu may specify already existing panel with `--panel` option

```sh
php brocooly new:customizer:section SectionName --panel PanelName
```

This will connect `SectionName` as a child of `PanelName`

!> Both Sections and Panels have to be registered within `config/customizer.php` file to have effect.

## Options

Options are no longer supports by Brocooly native classes - now you need to register custom settings within any service provider using native Kirki syntax