# Local PHP Development Environment

![Docker Logo](https://www.gravatar.com/avatar/def8e498c0e2b4d1b0cb398ca164cddd?s=115) ![The Wilds Logo](https://avatars1.githubusercontent.com/u/11079956?v=3&s=115)

The web development team [The Wilds](http://wilds.org) has decided to set up our
local development environments through the orchestration of Docker containers. This
has allowed us to develop on different systems while maintaining some consistency
in how the development environments are configured.

Prerequisites
----

* **A working install of [Docker](https://www.docker.com/products/docker)**. This
  will work under Windows, Mac OS, or Linux.
  
  **Note:** If you have Windows 10, we recommend using the stable version of
  Docker for Windows over the Docker Toolbox. It is a cleaner solution.
  
* **Docker Compose**. This should be installed with Docker, but we're putting it
  here for completeness.
  
* **Knowledge of your Docker machine IP address**. This is the IP address that is
  assigned to the network interface on your host computer that allows your host
  computer to communicate to the Docker machine. Here are some of the standard
  ones.
  
  | Environment        | IP             | Comment                   |
  |--------------------|----------------|---------------------------|
  | Docker for Windows | 10.0.75.1      | Default Hyper-V host IP   |
  | boot2docker        | 192.168.99.100 | Default docker-machine IP |
  
  We will refer to this address later in the document as the `$DOCKER_MACHINE_IP`.

Getting Started
----

1) Download the [`docker-compose.yml`](https://github.com/wildscamp/docker-localphpdevenvironment/blob/master/docker-compose.yml)
   from this repo.

2) Modify the appropriate environment variables inside your local copy of the
   `docker-compose.yml` file.
   * Set `PASV_ADDRESS` and `XDEBUG_REMOTE_HOST` to the `$DOCKER_MACHINE_IP`.
     See the README files on those containers
     [here](https://github.com/wildscamp/docker-vsftpd) and
     [here](https://github.com/wildscamp/docker-php) for details on these two
     variables.
   
3) Open incoming TCP port `9000` on your development machine (presumably the
   computer hosting Docker) for accepting Xdebug events from the
   [wildscamp/php](https://hub.docker.com/r/wildscamp/php/) container.
   
   **Note:** On Windows the Hyper-V `DockerNAT` connection can be classified
   as a Public network, so make sure to include the Public networking profile
   in the firewall rule. Also, the Local Scope can be limited to the
   `$DOCKER_MACHINE_IP`.

4) Create some named Docker volumes for storing the application files. This only
   needs to be done the first time you set up your environment.
   
   ```bash
   # Volume for web app files
   docker volume create --name docker-html

   # Volume for MySQL DB
   docker volume create --name docker-mysql

   # Volumes for certificates
   docker volume create --name docker-certificates
   ```

5) Start the environment. While inside the folder containing the
   `docker-compose.yml` file run the following command:
   
   ```bash
   docker-compose up
   ```
   
   The first time it will take a while for Docker to download and then start the
   containers. After this, starting up the environment should be much faster.
   Stopping all the containers can be accomplished by hitting the `Ctrl + C` key
   sequence in the terminal that is running the `docker-compose up` command.

Application Usage
----

Once the containers are up and running, they can be accessed through a few
different ways.

### MySQL

There are two ways to access the MySQL databases.

1) If you have a client MySQL browser (like 
   [PhpStorm's Database Browser](https://www.jetbrains.com/help/phpstorm/2016.2/working-with-the-database-tool-window.html)),
   you can connect directly to the database using the `$DOCKER_MACHINE_IP` and
   port `3306`.

2) The `docker-compose.yml` file is configured to spin up a PhpMyAdmin instance
   with access to the MySQL server. You can access it at
   `http://$DOCKER_MACHINE_IP:8080/`.

**Account Info**

| Username | Password | Database Name |
|----------|----------|---------------|
| root     | pw       | local_db

### Web App Access

Accessing the web application can be done simply by navigating to
`http://$DOCKER_MACHINE_IP/`.

### FTP

Because we're using named Docker volumes for storing our application, database
and certificate files, the primary way to add and remove files from these data
volumes is through FTP. The `docker-compose.yml` file spins up a container that
gives FTP access to these data volumes.

**Connection Info**

| Server             | Port |
|--------------------|------|
| $DOCKER_MACHINE_IP | 21   |

**FTP Users**

| Username | Password | Volume Access       |
|----------|----------|---------------------|
| www-data | ftp      | docker-html         |
| certs    | certs    | docker-certificates |
| mysql    | mysql    | docker-mysql        |