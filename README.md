# Local PHP Development Environment

![Docker Logo](https://www.gravatar.com/avatar/def8e498c0e2b4d1b0cb398ca164cddd?s=115) ![The Wilds Logo](https://avatars1.githubusercontent.com/u/11079956?v=3&s=115)

The web development team [The Wilds](http://wilds.org) has decided to set up our
local development environments through the orchestration of Docker containers. This
has allowed us to develop on different systems while maintaining some consistency
in how the development environments are configured.

## Prerequisites

1) **A working install of [Docker Desktop](https://www.docker.com/products/docker-desktop)**. This
   will work under Windows, Mac OS, or Linux.
  
2) **Docker Compose**. This should be installed with Docker, but we're putting it
   here for completeness.

3) (Recommended) **Docker Desktop using WSL 2 backend on Windows**. This is recommended
   for performance. `docker-comose up` would be run from inside WSL 2 and mapping folders from
   WSL 2 instead of from Windows.

## Getting Started

1) Download the [`docker-compose.yml`](https://github.com/wildscamp/docker-localphpdevenvironment/blob/master/docker-compose.yml)
   and [`docker-compose.override.yml`](https://github.com/wildscamp/docker-localphpdevenvironment/blob/master/docker-compose.override.yml)
   from this repo.

2) Modify the appropriate environment variables inside your local copy of the
   `docker-compose.override.yml` file. See the appropriate container definitions for details
   on specific environment variables.

   * [wildscamp/docker-php](https://github.com/wildscamp/docker-php)
   * [wildscamp/docker-phpmyadmin](https://github.com/wildscamp/docker-phpmyadmin)
   * [wildscamp/docker-certbot-route53](https://github.com/wildscamp/docker-certbot-route53)
   * [mysql/mysql](https://hub.docker.com/_/mysql)

3) Open incoming TCP port `9000` on your development machine (presumably the
   computer hosting Docker) for accepting Xdebug events from the
   [wildscamp/php](https://hub.docker.com/r/wildscamp/php/) container. You may be
   able to narrow down the scope of this rule by identifying which IP subnet the
   containers are running in.

4) Start the environment. While inside the folder containing the
   `docker-compose*.yml` files run the following command:

   ```bash
   docker-compose up
   ```

   > If you are using WSL 2, it is highly recommended to run this command from
   your project folder inside of the WSL 2 shell. The folder mapping is much faster
   from WSL 2 than from Windows.

   The first time it will take a while for Docker to download and then start the
   containers. After this, starting up the environment should be much faster.
   Stopping all the containers can be accomplished by hitting the `Ctrl + C` key
   sequence in the terminal that is running the `docker-compose up` command.

## Application Usage

Once the containers are up and running, they can be accessed through a few
different ways.

### MySQL

There are two ways to access the MySQL databases.

1) If you have a client MySQL browser (like
   [PhpStorm's Database Browser](https://www.jetbrains.com/help/phpstorm/settings-tools-database.html)),
   you can connect directly to the database using the `localhost` or `127.0.0.1` and
   port `3306`.

2) The `docker-compose.yml` file is configured to spin up a phpMyAdmin instance
   with access to the MySQL server. You can access it at
   `http://localhost:8080/`.

   **Account Info**

   | Username | Password | Database Name |
   |----------|----------|---------------|
   | root     | pw       | local_db

### Web App Access

Accessing the web application can be done simply by navigating to
`http://localhost/`. If you've defined a DNS name that points to
`127.0.0.1`, you could use that as well instead of the IP directly.

This also supports HTTPS if you have defined some certificates. The
`docker-compose.override.yml` file gives an example of using a LetsEncrypt cert.
