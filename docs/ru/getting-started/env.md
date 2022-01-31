# Окружение :id=environment

Окружение определяет настройки сайта в зависимости от среды разработки. Так, например, окружение для локальной разработки должно отличаться от окружения готового сайта - например, в локальном окружении позволено включение функций дебагинга сайта, отключение кэширования и индексации сайта, прочее

## Файлы конфигурации :id=config

Глобально за конфигурацию сайта отвечает файл `config/application.php`. Он содержит в себе список переменных WordPress, которые обычно содержатся внутри файла `wp-config.php` и служит как одна из входных точек для запуска сайта. Переменные, определенные здесь, будут доступны глобально по всему сайту, однако есть возможность переписать их значение под различное окружение

Внутри папки `config/environments` содержатся файлы окружения, который переписывают дефолтные настройки глобального окружения. Здесь Вы можете указать различные настройки, которые будут доступны только если окружение сайта совпадает с названием файла. То есть, если Ваше текущее окружение `development`, за настройки этого окружения будет отвечать файл `development.php` и так далее

> Окружение задается значением переменной `WP_ENV` внутри файла `.env`. Никаких ограничений по наименованию файлов и переменных нет, однако рекомендуется следовать тому же неймингу, что и `WP_ENVIRONMENT` и `wp_get_environment_type()`

## Файл окружения :id=env

Список всех переменных и их значение внутри файла конфигурации `.env` приведено в таблице ниже. Жирным выделены обязательные параметры

| Переменная | Определение |
| ------ | ------ |
| **DB_NAME** | Имя базы данных |
| **DB_USER** | Пользователь базы данных |
| **DB_PASSWORD** | Пароль пользователя базы данных |
| **DB_HOST** | Хост базы данных. `localhost` в большинстве случаев, `mysql` при использовании с Docker |
| DB_PREFIX | Префикс таблиц базы данных. По-умолчанию это `wp_`, однако в целях безопасности рекомендуется сменить на другое значение |
| **WP_ENV** | Окружение сайта |
| **WP_HOME** | УРЛ вашего сайта (адрес, включая протокол http или https). Используется внутри конфигурации `webpack.mix.js` как прокси сайта |
| **WP_SITEURL** | УРЛ корня вашего сайта. По сути адрес, где установлено ядро WordPress. По-умолчанию, равен адресу `WP_HOME` с последующим корнем `/wp`  |
| DOMAIN | Имя домена. Требуется только при запуске через Docker |
| WP_DEBUG_LOG | Путь к файлам логов или же `false`, если нужно их отключить |
| MAIL_MAILER | Дефолтный отправщик сообщений. По-умолчанию, используется нативная функция `wp_mail()` |
| MAIL_HOST - MAIL_ENCRYPTION | Настройки SMTP хоста для отправки сообщений |
| MAIL_FROM_ADDRESS, MAIL_FROM_NAME | Адрес и имя отправителя письма (From) |
| AUTH_KEY - NONCE_SALT | WordPress "соли" |