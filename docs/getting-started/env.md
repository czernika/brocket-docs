# Environment :id=environment

Environment defines sites setting depends on development stage. If you're on a local stage your settings may (and should) differ from production stage such as caching, debugging and more

## Configuration files :id=config

File `config/application.php` is responsible for site configuration. It is contains list of WordPress definitions and major settings which usually listed within `wp-config.php`. All defined here will be available globally across any environment type unless being redefined within appropriate config environment file

All environment configs lies within `config/environments` folder. Here you may specify any config settings within file which name is same as environment type. This means if your current environment is `development` than `development.php` the file and so on

> Environment type defined by `WP_ENV` variable within `.env` file. There are no any restrictions on naming conventions but it is required to follow same conventions as  `WP_ENVIRONMENT` and `wp_get_environment_type()` do

## Environment file :id=env

List if all variables and their definitions are listed in a table below `.env`. Required are bold

| Variable | Definition |
| ------ | ------ |
| **DB_NAME** | Database name |
| **DB_USER** | Database user |
| **DB_PASSWORD** | Database password |
| **DB_HOST** | Database host. It is `localhost` in most cases, `mysql` when using Docker |
| DB_PREFIX | Database tables prefix By default it is `wp_` but you should change it |
| **WP_ENV** | Site environment |
| **WP_HOME** | Site URL (protocol included). Used within `webpack.mix.js` config file as proxy |
| **WP_SITEURL** | Web root URL. An address where WordPress core is. Same as `WP_HOME` with `/wp`  |
| DOMAIN | Domain name. Required only if you are using Docker |
| WP_DEBUG_LOG | Path to log files or `false` if you need to disable logs |
| MAIL_MAILER | Default mailer. By default native `wp_mail()` function used |
| MAIL_HOST - MAIL_ENCRYPTION | SMTP host settings |
| MAIL_FROM_ADDRESS, MAIL_FROM_NAME | Sender address and name |
| AUTH_KEY - NONCE_SALT | WordPress "salts" |
