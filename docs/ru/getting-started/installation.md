# Установка :id=installation

## Установка через Composer :id=composer

Чтобы установить последний релиз Brocooly Framework в папку `<your-project-folder>` (или `.`, если Вы уже внутри нужной папки) запустите следующую команду:

```
composer create-project czernika/brocket <your-project-folder>
```

Далее внутри файла `.env` [заполните](ru/getting-started/env.md?id=env) необходимые данные для подключения к базе данных и веб-адрес сайта

Затем необходимо создать базу данных. Если у Вас установлена утилита wp-cli, все, что Вам потребуется для этого, это выполнить следующую команду

```sh
wp db create
```

Это создаст базу данных `DB_NAME` с префиксом `DB_PREFIX` для всех таблиц

Далее проследуйте по адресу `WP_HOME` в браузере и завершите установку или сделайте это при помощи  `wp core install ...` команды (требует дополнительные параметры).

> Вы можете сгенерировать соли WordPress - просто перейдите по ссылке [https://roots.io/salts.html](https://roots.io/salts.html) и вставьте сгенерированные ключи в файл `.env` или используйте команду `wp dotenv salts generate`, которая доступна при установки [этого](https://github.com/aaemnnosttv/wp-cli-dotenv-command) пакета WP CLI

Чтобы иметь полный доступ ко всем возможностям плагина [Query Monitor](https://querymonitor.com/) (который устанавливается по умолчанию вместе с ядром WordPress), запустите [данную](https://github.com/johnbillion/query-monitor/wiki/db.php-Symlink#using-wp-cli) команду
```sh
wp qm enable
```

Авторизуйтесь по адресу `wp/wp-login.php` и на этом все!

## Запуск вместе с Docker :id=docker

Brocket поставляется вместе с файлом `docker-compose.yml` - Вам нужно лишь установленный [Docker](https://www.docker.com/) на Вашем компьютере. После установки сайта, запустите контейнер

```sh
docker-compose up -d --build
```

!> Для запуска в Docker переменная `DB_HOST` должна иметь значение имени образа, то есть, `mysql`

Далее измените разрешения и пользователя на папку `web/app/uploads`. Это нужно для того, чтобы пользователь имел возможность загружать файлы через медиа-загрузчик

```sh
docker exec -it brocket-php bash

chown -R www-data:www-data /var/www/html/web/app/uploads

chmod -R 755 /var/www/html/web/app/uploads
```

Чтобы запустить сайт по https протоколу, установите [mkcert](https://github.com/FiloSottile/mkcert) на Вашей машине, проследуйте первичным настройкам, а затем внутри папки `docker/nginx/certs` запустите

```sh
mkcert domain.name
```

Это создаст два ключа для сайта domain.name - закрытый и открытый - которые Вы и должны подключить в конфигурации `docker/nginx/default.conf`.

!> Если Вы видите ошибку `SSL_ERROR_RX_RECORD_TOO_LONG`, откройте сайт не по адресу `localhost`, а по `127.0.0.1`

Конечно, Вы можете изменить конфигурацию как nginx, так и файла Docker в целом

## Настройка wp-cli :id=wp-cli

Для лучшей работы с сайтами на WordPress, установите утилиту [wp-cli](https://wp-cli.org).

В корне проекта уже есть файл `wp-cli.yml`, который уже настроен под работу на сайте со структурой Bedrock. По-умолчанию, он запрещает удалять базу, ставить плагины и создавать новых пользователей через консольную команду. Чтобы нормально работать в локальной среде, скопируйте этот файл, переименуйте в `wp-cli.local.yml` и уберите ненужные команды

Этот файл будет иметь приоритет по сравнению с `wp-cli.yml`. По сути, его контент будет следующим

```yml
# wp-cli.local.yml

path: web/wp
server:
  docroot: web
```

> `wp-cli.local.yml` уже прописан в `.gitignore`, потому не следует опасаться его случайного попадания на рабочее окружение при деплое