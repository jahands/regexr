VERSION --raw-output 0.8
PROJECT jahands/docker

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
	SAVE ARTIFACT ./build/

build-docker:
	FROM httpd:2.4-bookworm
	RUN apt-get update && apt-get install -y \
		php \
		libapache2-mod-php \
		php-mysql \
		&& rm -rf /var/lib/apt/lists/*
	COPY +build-gulp/build/ /usr/local/apache2/htdocs/
	COPY .htaccess /usr/local/apache2/htdocs/
	SAVE IMAGE regexr:latest
