VERSION --raw-output 0.8
PROJECT jahands/docker

composer:
	FROM composer/composer:latest-bin
	SAVE ARTIFACT /composer

install-deps:
	FROM node:10.21.0-stretch-slim
	WORKDIR /app
	COPY package.json package-lock.json .
	RUN npm install

build-gulp:
	FROM +install-deps
	COPY . .
 	ENV NODE_ENV=production
	RUN npx gulp deploy
	SAVE ARTIFACT .

build-composer:
	FROM php:8-apache
	WORKDIR /app
	COPY +composer/composer /usr/local/bin/composer
	COPY +build-gulp/app .
	RUN ls -lha
	RUN cd ./server && composer install
	RUN cp -r ./server/vendor ./build/server/vendor
	SAVE ARTIFACT ./build/

build-docker:
	FROM php:8-apache
	RUN docker-php-ext-install mysqli
	COPY +build-composer/build/ /var/www/html/
	RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
	# RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini" # todo: switch to production
	COPY .htaccess /var/www/html/
	COPY apache.conf /etc/apache2/sites-enabled/000-default.conf
	RUN a2enmod rewrite
	SAVE IMAGE regexr:php
	SAVE IMAGE --push gitea.uuid.rocks/docker/regexr:php
