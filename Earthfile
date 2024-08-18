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
	FROM php:7-apache
	WORKDIR /app
	COPY +composer/composer /usr/local/bin/composer
	COPY +build-gulp/app .
	RUN ls -lha
	RUN cd ./server && composer install
	RUN cp -r ./server/vendor ./build/server/vendor
	SAVE ARTIFACT ./build/

build-docker:
	FROM php:7-apache
	COPY +build-composer/build/ /var/www/html/
	# RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
	RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini" # todo: switch to production
	# COPY .htaccess /var/www/html/
	SAVE IMAGE regexr:php
