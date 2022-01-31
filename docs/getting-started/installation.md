# Installation :id=installation

## Via Composer :id=composer

To install fresh copy of Brocket Framework simply run next command:

```
composer create-project czernika/brocket <your-project-folder>
```

Next fill in `.env` file at the root of your project with correct information, at least:

| Variable | Value |
| ------ | ------ |
| DB_NAME | database name |
| DB_USER | database user |
| DB_PASSWORD | database password |
| WP_HOME | Home URL of your site |
| DOMAIN | Domain name. Only if you use Docker to launch site |

Create database. If you have wp-cli, all you need to do is

```sh
wp db create
```

This will create database named `DB_NAME` with `DB_PREFIX` prefixes for all tables.

Next go to `WP_HOME` address and finish the installation of WordPress or do it with `wp core install ...` command.

> You may generate WordPress salts - simply go to [https://roots.io/salts.html](https://roots.io/salts.html) and paste generated keys into `.env` file or use `wp dotenv salts generate` command with [this](https://github.com/aaemnnosttv/wp-cli-dotenv-command) WP CLI package

To have full access for [Query Monitor](https://querymonitor.com/) plugin options you may [run](https://github.com/johnbillion/query-monitor/wiki/db.php-Symlink#using-wp-cli)
```sh
wp qm enable
```

Login into admin-panel under `wp/wp-login.php` and that's it. Happy coding!

## Run with Docker :id=docker

Brocket comes with `docker-compose.yml` file - you need Docker installed on your machine. After Brocket app has been installed run container with

```sh
docker-compose up -d --build
```

After that you need to change permissions for `web/app/uploads` folder to let user to be able to upload media with WordPress' media-uploader

```sh
docker exec -it brocket-php bash

chown -R www-data:www-data /var/www/html/web/app/uploads

chmod -R 755 /var/www/html/web/app/uploads
```

To run site with https-protocol install [mkcert](https://github.com/FiloSottile/mkcert), proceed its installation and next run `mkcert domain.name` within `docker/nginx/certs` directory. This will create pair of keys - which you need to include within `docker/nginx/default.conf` file 

!> If you see `SSL_ERROR_RX_RECORD_TOO_LONG`, open website no at `localhost` but `127.0.0.1`

## wp-cli :id=wp-cli

To improve your use experience install [wp-cli](https://wp-cli.org).

There is `wp-cli.yml` file which ready to work with Bedrock folder structure. By default you cannot drop database, install plugins and create any users. To work normally within local environment copy this file, rename it into `wp-cli.local.yml` and remove any command

This file will take priority over `wp-cli.yml`. His content will be

```yml
# wp-cli.local.yml

path: web/wp
server:
  docroot: web
```

> `wp-cli.local.yml` already present in `.gitignore` file so there is no need to worry about having it in a production via deploy