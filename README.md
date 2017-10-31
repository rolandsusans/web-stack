# Docker Web Stack

[![Build Status](https://travis-ci.org/jpcaparas/docker-web-stack.svg?branch=master)](https://travis-ci.org/jpcaparas/docker-webapp)

# Overview

Docker Compose makes it easy to interconnect various Docker images. What better way to showcase its features than by
creating (yet another) LEMP stack. This is one is much more bleeding edge than others, though :wink:.

# What's inside

A `docker-compose`-powered stack to get your PHP project running in no time.

- PHP-FPM 7.1
- nginx
- MariaDB
- Node w/ Yarn


# Requirements

- `Docker` v17.09.0-ce or higher
- `docker-compose` v1.8.0 or higher


# How-to (simple)

To start the stack, run:

        docker-compose up -d --build --remove-orphans

- The `-d` flag daemonises the stack.
- The `--build` builds services (e.g. `php-fpm`, `nginx`) that comprise the stack.
- The `--remove-orphans` stack ensures that services that aren't being used are deleted (to save disk space).

Provided that no errors were emitted during the start, you should be able to visit your browser on `http://localhost:8080`.

---

To stop the stack, run:

        docker-compose stop

To ensure that all services have stopped, run:

        docker-compose ps

There should be no more services running.

---

To connect to the MariaDB service, run:

        docker-compose exec mariadb mysql -uapp -p

- This command uses the `app` user. There is also a `root` user.
- Use the password defined on the `docker-compose.yml` file.

---

To install composer dependencies, run:

        docker-compose run --rm composer <install|update|require...>

- The `--rm` flag ensures that that intermediate (temporary) containers are deleted once you install packages (to save disk space).
- The service's data is mounted on the `./mariadb` host folder, which means that data will persist between `docker-compose` `up`s and `stop`s.


---

To install npm packages, run:

        docker-compose run --rm yarn add [name-of-package]

- `yarn` is faster than `npm` and contains a lockfile (`yarn.lock`), for deterministic dependency resolution.


# How-to (advanced)

To enable the Xdebug module:

1. Copy the `.env.example` to an `.env` file and:
    1. Change the value of `XDEBUG_ON` to `true`
    1. Change the value of `XDEBUG_REMOTE_HOST` to your host machine's IP address.
1.  Run `docker-compose up -d --build --remove-orphans` to re-build the service.

---

If you want to extend the functionality of a service (e.g. php-fpm), you have to re-build it.

To accomplish this, modify the Dockerfile, then run:

        docker-compose build --no-cache --force-rm [name-of-service]

... followed by a `docker-compose up [arguments...]`

---

If you want to use a different web server port (e.g. 80), modify the port on the `docker-compose.yml` file
and start the stack again.


# Gotchas

Ensure that the host machine ports `8080` (web server) and `3306` (database) are open on your host machine.

---

Your version of Docker for Linux might ship with v1.8 (an older release) of Docker Compose. You need to upgrade
to at least v1.12 in order to make this stack work.

For Linux/macOS users, you can run:

        curl -L https://github.com/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > /usr/bin/docker-compose
        chmod +x /usr/bin/docker-compose

To check that you are using v1.12 or higher, run:

        docker-compose --version


# Warranties

- This stack was built on `Ubuntu 16.04.3 LTS 64-bit`.


# Attributions

- This project was greatly inspired by the [Docker web stack](https://github.com/jpcaparas/docker-web-stack).

