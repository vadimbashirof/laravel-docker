### Docker для Laravel

**Версия PHP**  

Версию php определяем в файле `docker-compose.yml` в блоке `php-fpm` в аргументе `phpversion`
необходимо писать полную версию например `8.1.0` или в `.env` файле в аргументе `PHP_VERSION`

***

**Собрать и запустить докер**  

`docker composer build`  
`docker composer up`  

***

**Установка Laravel**  

запускаем docker `docker composer up`  
и заходим в контейнер с php-fpm:  
`docker compose exec php-fpm bash`  
устанавливаем laravel:  
`composer create-project laravel/laravel app-laravel`  
после установки перемещаем все файлы из папки app-laravel в корневую директорию и удаляем пустую папку:  
`shopt -s dotglob` добавляем возможность переноса скрытых файлов  
`mv app-laravel/* ./` перенос файлов  
`rm -rf app-laravel` удаление пустой папки  

***

**Настройка Laravel**  

Создаем файл `.env`:  
`mv .env.example .env`  
`php artisan key:generate`

***

**Настройка Баз данных**

В .env файл добавляем данные
```
DB_CONNECTION=pgsql

DB_HOST=pgsql
DB_PORT=5432
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=sectet

DB_LOGS_HOST=pgsql
DB_LOGS_PORT=5432
DB_LOGS_DATABASE=laravel-logs
DB_LOGS_USERNAME=laravel
DB_LOGS_PASSWORD=secret
```
Если будем менять названия баз данных то нужно изменить их инициализацию
в файле `docker/postgres/initdb.d/init.sql` там же можно дабавить установку модулей

В папку `docker/postgres/initdb.d/` можно добавлять файлы дампов базы данных.

***

**Настройка redis**

В `.env` файл добавляем данные
```
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379
```

***

**Настройка mailhog**

В `.env` файл добавляем данные
```
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
```

для проверки писем заходить на адресс <http://localhost:8025/>

***

**Настройка storage**

чтобы при разработке было разрешение на сохранение файлов  
`docker compose exec php-fpm bash`  
`chmod -R 777 storage/`  

***

**Настройка xdebug для phpstorm**  

1. заходим в настройки `File -> Settings -> PHP -> Servers` нажимаем на значак плюса `+`
2. добавляем новый сервер  
   `name: docker`  
   `host: 127.0.0.1`  
   нажимаем галочку `Use path mappings`
   укажем расположение файлов внутри контейнера для папки проекта `Absolute path on the server: /var/www`
3. сверху в панели управления, рядом с кнопкой жучка дебага нажать на выпадающий список и выбираем Edit configurations
4. нажимаем `+` и добавляем `PHP Remote Debug`
5. добавляем конфигурацию
   `name: debug`
   нажимаем галочку `Filter Debug connection by IDE key`  
   `Server: docker` - имя сервера который создавали в предыдущем шаге  
   `IDE key: PHPSTORM` - этот параметр можно посмотреть в файле `docker/php-fpm/xdebug.ini` в параметре `xdebug.idekey`

***
