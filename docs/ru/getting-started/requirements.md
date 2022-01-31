# Минимальные требования :id=requirements

Чтобы корректно работать с Brocket Framework, Ваш сервер должен отвечать следующим минимальным требованиям:

- Версия **PHP 8.0** или выше;
- Установленный пакетный менеджер **[Composer](https://getcomposer.org/)**;
- Минимальные [требования](https://wordpress.org/about/requirements/) для установки **WordPress**;
- Корень сервера должен указывать на папку `web`, так как это [требование](https://roots.io/docs/bedrock/master/server-configuration/#nginx-configuration-for-bedrock) структуры `roots/bedrock` (схожие требования у Laravel с папкой `public`);
- Рекомендуется: командная утилита для работы с WordPress [wp-cli](https://wp-cli.org/).