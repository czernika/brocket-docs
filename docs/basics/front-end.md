# Работа с фронтом сайта :id=frontend

## Файлы представлений :id=views

Brocooly старается разделять логику приложения, и внешний вид сайта представлен в файлах `.twig` внутри директории `resources/views`. Данный путь можно изменить в настройках конфигурации `config/timber.php`, изменив ключ `views`

## Фильтры :id=filters

Помимо [фильтров Timber](), Brocooly предоставляет несколько своих. Чтобы добавить свои фильтры, в `boot` методе любого провайдера добавить коллбэк для фильтра `timber/twig`.

### zeroise :id=zeroise

Добавляет нули перед числом при необходимости

[Референс](https://developer.wordpress.org/reference/functions/zeroise/)

### antispambot :id=antispambot

Скрывает адрес Email от ботов

[Референс](https://developer.wordpress.org/reference/functions/antispambot/)