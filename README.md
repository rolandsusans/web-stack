# Docker Web Stack

[![Build Status](https://travis-ci.org/rolandsusans/web-stack.svg?branch=master)](https://travis-ci.org/rolandsusans/web-stack)
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
- `docker-compose` v1.12.0 or higher


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

Manage composer dependencies, run:

        docker-compose run --rm composer <install|update|require...>

- The `--rm` flag ensures that that intermediate (temporary) containers are deleted once you install packages (to save disk space).
- The service's data is mounted on the `./mariadb` host folder, which means that data will persist between `docker-compose` `up`s and `stop`s.


---

Manage npm packages with yarn, run:

        docker-compose run --rm yarn add [name-of-package]

- `yarn` is faster than `npm` and contains a lockfile (`yarn.lock`), for deterministic dependency resolution.
[Yarn documnetation](https://yarnpkg.com/en/docs/usage)

# How-to (advanced)

To enable the Xdebug module:

1. Copy the `.env.example` to an `.env` file and:
    1. Change the value of `XDEBUG_ON` to `true`
    1. Change the value of `XDEBUG_REMOTE_HOST` to your host machine's IP address.
2.  Run `docker-compose up -d --build --remove-orphans` to re-build the service.

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



# Setting up PHPStorm with XDebug

1. Find out your docker IP address

    ```bash
    ifconfig
    
    ```
    
    In my case it was:
    
    ```
    docker0   Link encap:Ethernet  HWaddr **:**:**:**:**:**  
              inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
              inet6 addr: ****::**:****:****:****/** Scope:Link
              UP BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:472 errors:0 dropped:0 overruns:0 frame:0
              TX packets:576 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:32312 (32.3 KB)  TX bytes:657698 (657.6 KB)
    
    ```
2. Coppy .env.example to .env and modify IP:
   
    ```
    XDEBUG_ON=true
    XDEBUG_REMOTE_HOST=172.17.0.1
    
    ```
    
3. Start/restart web-stack project by running
    ```
        docker-compose up -d --build --remove-orphans    
    ```
    
4. PhpStorm configuration
    1. IDE settings:
    ![Screen shot (note that port is 9000) ](https://imgur.com/4OLlNAs)
    2. Open project on your local machine and you should be prompted:
    ![Incomming connection from XDebug](https://imgur.com/fB98WXW)
    3. XDebug in action (note that your web page will not load until you step over all brake points)
    ![XDebug in action](https://imgur.com/GGpZEAX)

# Attributions

- This project was greatly inspired by the [Docker web stack](https://github.com/jpcaparas/docker-web-stack) and by [Laradock project](https://github.com/laradock/laradock)- 
