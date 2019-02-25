# Sulu-Docker

Comprehensive **development** environment for the [Sulu](https://sulu.io/) content management platform based on docker-compose.

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

* Sulu-Website: `PROJECT_DOMAIN:PORT_NGINX` (default: `sulu.localhost:10080`)
* Sulu-Admin: `PROJECT_DOMAIN:PORT_NGINX/admin` (default: `sulu.localhost:10080/admin`)
* MySQL: `PROJECT_DOMAIN:PORT_MYSQL` (default: `sulu.localhost:13306`)
* Kibana: `PROJECT_DOMAIN:PORT_KIBANA` (default: `sulu.localhost:15601`)

## Folder Structure

* `project`: contains project files 
* `docker`: configuration for docker containers
* `var/data`: folder for application related data
* `var/logs`: log files of different services

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
composer create-project "sulu/sulu-minimal" /tmp/project
cp -RT /tmp/project . && rm -rf /tmp/project/
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

After completing these steps the services should be accessible via the URLs listed above.

## Update Container Configuration

When changing the configuration of a docker container inside `docker` folder, the environment must be rebuilt and restarted:

```bash
docker-compose down
docker-compose build
docker-compose up
```

## Xdebug Configuration

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
