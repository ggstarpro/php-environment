# 構成
* nginx: 1.21.6-alpine
* alpine: 3.15
* mysql: 8.0.28
* minio: latest
* mailhog: 1.0.1

# 環境構築手順
* docker-compose up -d
* docker exec -it bas_php bash
* cd /usr/share/nginx/html
* composer create-project laravel/laravel bas-app (最初に作る人のみ)
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
* http://localhost/ を表示確認


# minioについて
* 以下のような形で扱うことができます
```
// 簡易のため直接指定してます
public function __construct()
{
    $this->s3= new S3Client([
        'version' => 'latest',
        'region' => 'us-east-1',
        'endpoint'=> 'http://bas-minio:9999',
        'use_path_style_endpoint' => true,
        'credentials' => [
            'key' => 'bas',
            'secret' => 'password',
        ],
    ]);
}

public function getObject($key)
{
    return $this->s3->getObject([
        'Bucket' => 'test',
        'Key'    => $key,
    ]);

}

public function index()
{
    $pizzaJpg = $this->getObject('test.jpg');
    header('Content-type: image/jpeg');
    echo $pizzaJpg['Body'];
    exit();
}
```