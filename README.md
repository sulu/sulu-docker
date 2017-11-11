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

* `app.dev:10080`: Sulu-Website
* `app.dev:10080/admin`: Sulu-Admin
* `app.dev:19200`: Elasticsearch
* `app.dev:15601`: Kibana
* `app.dev:19600`: Logstash

## Installation

```bash
git clone https://github.com/wachterjohannes/sulu-docker
cd sulu-docker
composer create-project "sulu/sulu-minimal" app
docker-sync-stack start
```

## Initialize Sulu

```bash
docker-compose exec php bash
bin/adminconsole sulu:build dev --destroy
```

## Folder Structure

* `app`: contains application files 
* `config`: configuration for docker containers
* `var/data`: folder for application related data
* `var/logs`: log files of different services
* `var/tmp`: temporary files of application

## Developer Experience problems

* `rm -rf var/cache/*` only inside container?
* Sometimes you have to hard clear the cache (maybe permissions?)
* Development outside of container?
  - PHP and composer are required then also 
