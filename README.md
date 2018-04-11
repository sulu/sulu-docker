# Sulu-Docker

This docker environment is build as a wrapper for the sulu/sulu-minimal and will allow isolated development.

## Prerequisites

* [Docker](https://docs.docker.com/engine/installation/)

## Services

* Nginx
* Mysql
* PHP-FPM
* Elasticsearch
* Kibana
* Logstash

## Features

* Autoconfiguration of environment (Nginx, MySQL and PHP-FPM)
* TMP-Volume for symfony cache
* ELK stack for log processing (nginx logs and symfony logs)
* Xdebug debugging
* Profiling with Blackfire

## URLs

* `app.lo:10080`: Sulu-Website
* `app.lo:10080/admin`: Sulu-Admin
* `app.lo:13306`: MySQL
* `app.lo:15601`: Kibana

## Installation

```bash
git clone https://github.com/wachterjohannes/sulu-docker
cd sulu-docker
cp .env.dist .env
```

The environment variables configures the whole docker stack. You can set here the path to your project, mysql-database php-settings and the public ports of the services.

Add a host entry to `/etc/hosts` (use domain name from `.env` file):

```
127.0.0.1    app.lo
```

## Run container

```bash
docker-compose up
```

Or in background with:

```bash
docker-compose start
```

## Initialize Sulu

```bash
docker-compose exec php bash
composer create-project "sulu/sulu-minimal" .
```

To initialize the `app/config/parameters.yml` file use following database config values:

```yml
parameters:
    database_driver: pdo_mysql
    database_host: mysql
    database_port: null
    database_name: mydb
    database_user: user
    database_password: userpass
```

```bash
bin/adminconsole sulu:build dev --destroy
```

## Update container

When you change the configuration of the docker container inside `config` folder you have to build the container before
restart them.

```bash
docker-compose build
```

## Xdebug configuration

Make sure to read the comments in `.env`, you'll need to set the `XDEBUG_REMOTE_CONNECT_BACK` or `XDEBUG_REMOTE_HOST` variable.

Also don't forget to configure PHPSTorm:
Project preferences -> Languages & Frameworks -> PHP -> Servers:
Add a server: 
Name: sulu-docker *Important:* Only use sulu-docker, because else it will NOT work.
"Use path mappings"
Absolute path on the server: /var/www/project

## Folder Structure

* `project`: contains project files 
* `config`: configuration for docker containers
* `var/data`: folder for application related data
* `var/logs`: log files of different services
* `var/tmp`: temporary files of application

## Developer Experience problems

* `rm -rf var/cache/*` only inside container?
* Sometimes you have to hard clear the cache (maybe permissions?)
* Development outside of container?
  - PHP and composer are required then also 
