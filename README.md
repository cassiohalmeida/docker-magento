# Docker for Magento

This repository allows the creation of a Docker environment that meets [Magento 1](http://devdocs.magento.com/guides/m1x/system-requirements.html) requirements.

## Architecture

* `apache`: [httpd:2.4](https://github.com/ajardin/docker-magento/blob/master/apache/Dockerfile) custom image with Apache (web server).
* `maildev`: [djfarrelly/maildev:latest](https://hub.docker.com/r/djfarrelly/maildev/) image (emails debugging).
* `mysql`: [mysql:latest](https://hub.docker.com/_/mysql/) image (Magento database).
* `php` : [php:5.6-fpm](https://github.com/ajardin/docker-magento/blob/master/php/Dockerfile) custom image with PHP-FPM.

## Additional Features

Since this environment is designed for a local usage, it comes with features helping the development workflow.

The `apache` and `php` containers have a mount point used to share source files.
By default, the `~/www/` directory is mounted from the host. It's possible to change this path by editing the `docker-compose.yml` file.

It's also possible to add custom virtual hosts: all `./apache/vhosts/*.conf` files are copied in the Apache directory during the image build process.

And the `./php/custom.ini` file is used to customize the PHP configuration during the image build process.

## Installation

This process assumes that [Docker Engine](https://www.docker.com/docker-engine) and [Docker Compose](https://docs.docker.com/compose/) are installed.
Otherwise, you should have a look to [Install Docker Engine](https://docs.docker.com/engine/installation/) before proceeding further.

### Clone the repository

```bash
$ git clone git@github.com:ajardin/docker-magento.git magento1
```

It's also possible to download it as a [ZIP archive](https://github.com/ajardin/docker-magento/archive/master.zip).

### Configure the environment variables

```bash
$ make env
```

### Configure the new .env file with your variables

```bash
##### PROJECT ROOT PATH
PROJECT_ROOT=/full/path/to/code

#### HOST IP
HOST_IP=172.16.18.88

##### APACHE
APACHE_PORT=80

##### MYSQL
MYSQL_ALLOW_EMPTY_PASSWORD=1
MYSQL_ROOT_PASSWORD=
MYSQL_USER=mysqluser
MYSQL_PASSWORD=123
MYSQL_DATABASE=database
MYSQL_PORT=3307


##### MAILDEV
MAILDEV_PORT=1080
```

### Build the environment

```bash
$ make install
```

### Check the containers

```bash
$ make ps
        Name                      Command               State              Ports
--------------------------------------------------------------------------------------------
magento1_apache_1      httpd-foreground                 Up      0.0.0.0:443->443/tcp, 80/tcp
magento1_blackfire_1   blackfire-agent                  Up      8707/tcp
magento1_maildev_1     bin/maildev --web 80 --smtp 25   Up      25/tcp, 0.0.0.0:1080->80/tcp
magento1_mongo_1       docker-entrypoint.sh mongod      Up      0.0.0.0:27017->27017/tcp
magento1_mysql_1       docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp
magento1_php_1         docker-custom-entrypoint p ...   Up      9000/tcp
magento1_redis_1       docker-entrypoint.sh redis ...   Up      6379/tcp
```

Note: You will see something slightly different if you do not clone the repository in a `magento1` directory.
The container prefix depends on your directory name.
