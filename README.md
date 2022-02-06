# 構成
nginx:1.21.6-alpine
alpine:3.15
mysql:8.0.28


# 環境構築手順
* docker-compose up -d
* docker exec -it bas_php bash
* cd /usr/share/nginx/html
* composer create-project laravel/laravel bas-app
* cd bas-app
* composer install
* php artisan key:generate
```
.envの書き換え
DB_CONNECTION=mysql
DB_HOST=bas_mysql
DB_PORT=3306
DB_DATABASE=bas
DB_USERNAME=bas
DB_PASSWORD=password
```
* php artisan migrate
* http://localhost/
