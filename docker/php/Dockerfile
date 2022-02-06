FROM alpine:3.15

# どこのフォルダの中でコマンドを実行するのかを記載
WORKDIR /usr/share/nginx/html

RUN apk update \
  && apk add bash curl \
  && adduser -D nginx \
  && chown -R nginx:nginx /usr/share/nginx/html \
  && apk add openrc \
  && mkdir /var/run/php-fpm /var/log/php-fpm \
  && chown nginx:nginx /var/run/php-fpm \
  && chown nginx:nginx /var/log/php-fpm \
  # php-fpm8これがないと起動の際にERROR: php-fpm8 failed to startと言われる
  && mkdir /run/openrc \
  && touch /run/openrc/softlevel

# /etc/init.d/php-fpm8  start
# rc-status
# apk search php | grep ctype
# netstat -al

# 必要なモジュールインストール (PHP 8)
RUN apk add --update --no-cache php8 php8-fpm php8-pdo_mysql \
  php8-curl \
  php8-opcache php8-pdo_odbc php8-pdo_mysql php8-gd \
  php8-pecl-apcu php8-mysqli php8-fpm \
  php8-zip php8-curl php8-phar php8-sockets php8-dom \
  php8-ftp php8-iconv php8-posix \
  php8-session php8-sodium php8-sqlite3 php8-tokenizer \
  php8-pear php8-pecl-imagick \
  php8-bcmath  php8-ctype php8-fileinfo php8-json \
  php8-mbstring php8-openssl php8-tokenizer \
  php8-xml php8-xmlreader php8-xmlwriter php8-simplexml \
  php8-intl

# phpコマンドを作成
RUN ln /usr/bin/php8 /usr/bin/php

# ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓ composer ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
# composer-setup.phpのハッシュ値
ARG composer_setup_hash=906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8

# インストール後のComposer のフォルダ名とファイル名
ARG composer_dir=/usr/local/bin
ARG composer_filename=composer

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('sha384', 'composer-setup.php') !== '$composer_setup_hash') { \
        echo '!!! Failed to install Composer !!!'; \
        unlink('composer-setup.php'); \
      } \
      echo PHP_EOL;" \
  && php composer-setup.php --install-dir=$composer_dir --filename=$composer_filename \
  # インストールした Composer のバージョン表示
  && composer -v | grep "Composer version" \
  # unzip コマンド (composer create-project で必要)
  && apk add unzip \
  && php -r "unlink('composer-setup.php');" \
  && chmod +x /usr/local/bin/composer
# ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑ composer ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

# 時間修正
RUN apk --no-cache add tzdata && \
  cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
  apk del tzdata

COPY ./docker/php/www.conf /etc/php8/php-fpm.d/www.conf
COPY ./docker/php/php.ini /etc/php8/php.ini
# COPY ./docker/php/www.conf /etc/php-fpm.d/www.conf.default

# php-fpmポート番号
EXPOSE 9000
# php-fpmのサービス起動
ENTRYPOINT /usr/sbin/php-fpm8 --nodaemonize