# Sulu-Docker

**Development environment** for the [Sulu](https://sulu.io/) content management platform built with 
[Docker Compose](https://docs.docker.com/compose/).

> Docker is a great tool for trying new technologies effortlessly. Unfortunately, there are still significant
> [filesystem performance issues](https://github.com/docker/for-mac/issues/1592) when using bind mounts on some 
> systems. 
>
> If you are experiencing bad performance, we recommend to use dockerized services (MySQL, Elasticsearch) in
> combination with the [Symfony Local Web Server](https://symfony.com/doc/current/setup/symfony_server.html).
> For example, the [Sulu Demo repository](https://github.com/sulu/sulu-demo) includes such a setup.

## URLs

* Sulu-Website: `PROJECT_DOMAIN:PORT_NGINX` (default: `sulu.localhost:10080`)
* Sulu-Admin: `PROJECT_DOMAIN:PORT_NGINX/admin` (default: `sulu.localhost:10080/admin`)
* MySQL: `PROJECT_DOMAIN:PORT_MYSQL` (default: `sulu.localhost:13306`)
* Elasticsearch: `PROJECT_DOMAIN:PORT_ELASTICSEARCH` (default: `sulu.localhost:19200`)

## Install Environment

```bash
git clone https://github.com/sulu/sulu-docker && cd sulu-docker
```

The `.env` file contains several environment variables that are used to throughout the environment. 
This allows to configure the project path, database settings, public ports of the services and the domain name.

To access your project via the configured domain, you need to add it to your `/etc/hosts` file:

```
127.0.0.1    sulu.localhost (value of your PROJECT_DOMAIN)
```

## Startup Containers

```bash
docker-compose up
```

You can also startup the containers in the background by executing:

```bash
docker-compose start
```

## Create Sulu Project

```bash
# Start bash inside of the php container
docker-compose exec php bash

# Create a new sulu project with composer
composer create-project sulu/skeleton /var/www/html

# Set the correct database url in the `.env.local` file
echo "DATABASE_URL=mysql://$MYSQL_USER:$MYSQL_PASSWORD@mysql:3306/$MYSQL_DATABASE" >> .env.local

# Initialize sulu project
bin/adminconsole sulu:build dev --destroy
```

After completing these steps the services are accessible via the URLs listed above.

## Update Container Configuration

When changing configuration inside of the `docker` folder, the environment must be rebuilt and restarted:

```bash
docker-compose down
docker-compose build
docker-compose up
```
