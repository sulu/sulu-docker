# Sulu-Docker

This docker environment is build as a wrapper for the sulu/sulu-minimal and will allow isolated development.

## Prerequisites

* [Docker](https://docs.docker.com/engine/installation/)
* [Docker-Sync](https://github.com/EugenMayer/docker-sync/wiki/1.-Installation)

## Services

* Nginx
* Mysql
* PHP-FPM (with synced application code)
* Elasticsearch
* Kibana
* Logstash

## Features

* Autoconfiguration of environment (Nginx, MySQL and PHP-FPM)
* Synchronization of application code to improve performance
* TMP-Volume for symfony cache
* ELK stack for log processing (nginx logs and symfony logs)

## Missing features

* XDebug
* Blackfire

## URLs

* `app.lo:10080`: Sulu-Website
* `app.lo:10080/admin`: Sulu-Admin
* `app.lo:13306`: MySQL
* `app.lo:15601`: Kibana

## Installation

```bash
cp .env.dist .env
```

The environment variables configures the whole docker stack. You can set here the path to your project, mysql-database
php-settings and the public ports of the services.

```bash
git clone https://github.com/wachterjohannes/sulu-docker
cd sulu-docker
composer create-project "sulu/sulu-minimal" project
docker-sync-stack start
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

Add a host entry to `/etc/hosts` (use domain name from `.env` file):

```
app.lo    127.0.0.1
```

## Initialize Sulu

```bash
docker-compose exec php bash
bin/adminconsole sulu:build dev --destroy
```

## Update container

When you change the configuration of the docker container inside `config` folder you have to build the container before
restart them.

```bash
docker-compose build
docker-sync-stack start
```

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
