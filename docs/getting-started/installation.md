# Installation

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

Create database. If you have wp-cli, all you need to do is
```sh
wp db create
```

This will create database named `DB_NAME` with `DB_PREFIX` prefixes for all tables.

Next go to `WP_HOME` address and finish the installation of WordPress or do it with `wp core install ...` command.

> You may generate WordPress salts - simply go to [https://roots.io/salts.html](https://roots.io/salts.html) and paste generated keys into `.env` file or use `wp dotenv salts generate` command with [this](https://github.com/aaemnnosttv/wp-cli-dotenv-command) WP CLI package

To have full access for Query Monitor plugin options you may run
```sh
wp qm enable
```

Login into admin-panel under `wp/wp-login.php` and that's it. Happy coding!
