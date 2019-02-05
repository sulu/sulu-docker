# Sulu-Docker

Comprehensive *development* environment for the [Sulu](https://sulu.io/) content management platform based on docker-compose.

## Prerequisites

* [Docker](https://docs.docker.com/engine/installation/)

## Services

* NGINX
* PHP-FPM
* MySQL
* Blackfire
* Elasticsearch
* Kibana
* Logstash

## Features

* Autoconfiguration of environment (Nginx, MySQL and PHP-FPM)
* Xdebug debugging
* Profiling with Blackfire
* ELK stack for log processing (nginx logs and symfony logs)

## URLs

* `sulu.localhost:10080`: Sulu-Website
* `sulu.localhost:10080/admin`: Sulu-Admin
* `sulu.localhost:13306`: MySQL
* `sulu.localhost:15601`: Kibana

## Installation

```bash
git clone https://github.com/wachterjohannes/sulu-docker
cd sulu-docker
cp .env.dist .env
```

The `.env` file contains several environment variables that are used to throughout the environment. 
This allows to configure the project path, database settings, php settings, public ports of the services and the domain name.

To access sulu, a host entry must be added to `/etc/hosts`:

```
127.0.0.1    sulu.localhost (value of your PROJECT_DOMAIN)
```

## Startup Containers

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
bin/adminconsole sulu:build dev --destroy
```

During the installation, use the following values for initializing th `app/config/parameters.yml` file:

```yml
parameters:
    database_driver: pdo_mysql
    database_host: mysql
    database_port: null
    database_name: sulu
    database_user: user
    database_password: password
    database_version: 5.7
```

## Update Configuration

When changing the configuration of a docker container inside `config` folder, the environment must be rebuilt and restarted:

```bash
docker-compose down
docker-compose build
docker-compose up
```

## Xdebug configuration

You will need to set the `XDEBUG_REMOTE_CONNECT_BACK` or `XDEBUG_REMOTE_HOST` variable file. Make sure to read the comments in `.env` file.

When using PHPStorm, you will need to add as new server with the following configuration at **Project preferences -> Languages & Frameworks -> PHP -> Servers:**

```
Name: sulu-docker
Host: value of your PROJECT_DOMAIN variable (default: sulu.localhost)
Port: value of your PORT_NGINX variable (default: 10080)
Debugger: Xdebug

Use path mappings: true
Absolute path on the server of the project directory: /var/www/project
```

## Folder Structure

* `project`: contains project files 
* `config`: configuration for docker containers
* `var/data`: folder for application related data
* `var/logs`: log files of different services

## Developer Experience problems

* `rm -rf var/cache/*` only inside container?
* Sometimes you have to hard clear the cache (maybe permissions?)
* Development outside of container?
  - PHP and composer are required then also 
