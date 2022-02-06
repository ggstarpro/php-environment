docker exec -it bas_php bash
cd /usr/share/nginx/html
composer create-project laravel/laravel bas-app
composer install
cd bas-app
php artisan key:generate