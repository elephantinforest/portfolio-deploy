FROM php:8.2-apache

WORKDIR /var/www/html

# PHP で必要なライブラリをインストール
RUN apt-get update  --fix-missing\
    && apt-get install -y libonig-dev libzip-dev unzip mariadb-client git \
    && docker-php-ext-install mbstring zip bcmath pdo_mysql mysqli \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug

# Node.jsとNPMのインストール
RUN apt-get install -y \
    curl \
    gnupg
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN apt-get install -y nodejs npm
RUN npm install npm@latest -g

RUN apt-get update && apt-get install -y \
    libfreetype-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
&& docker-php-ext-configure gd --with-freetype --with-jpeg \
&& docker-php-ext-install -j$(nproc) gd

# mod_rewrite モジュールを使えるようにする
RUN a2enmod rewrite

# composer のインストール
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER 1

# ファイルのコピー
COPY ./src /var/www/html
COPY ./docker/app/php.ini /usr/local/etc/php/php.ini
COPY ./docker/app/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
COPY ./docker/app/000-default.conf /etc/apache2/sites-enabled/000-default.conf

#環境変数の設定
ENV API_KEY=AIzaSyAgsMAuZkOuu9mMwANgdPHijQa2Bbm-zZg
ENV MYSQL_DATABASE=test_database
ENV MYSQL_PASSWORD=pass
ENV MYSQL_ROOT_PASSWORD=pass
ENV MYSQL_USER=test_user

# apacheのuidとgidをdocker user uid/gidに変更。
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

# プロジェクトディレクトリ内に移動してnpm installを実行
WORKDIR /var/www/html
RUN npm install
RUN composer install
# マイグレーションの実行（ファイルパスが正しいことを確認）
# RUN php /var/www/html/src/migration/initialize_all_table.php

# Apacheの設定ファイルをコピー
# COPY ./apache2.conf /etc/apache2/apache2.conf

RUN chown -R www-data:www-data /var/www/html
