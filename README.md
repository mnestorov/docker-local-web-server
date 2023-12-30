# Docker Local Web Server

[![Licence](https://img.shields.io/github/license/Ileriayo/markdown-badges?style=for-the-badge)](./LICENSE)

## Support The Project

Your support is greatly appreciated and will help ensure the project's continued development and improvement. Thank you for being a part of the community!

[![Stripe](https://img.shields.io/badge/Stripe-626CD9?style=for-the-badge&logo=Stripe&logoColor=white)](https://buy.stripe.com/7sI6qagF4cQV4xy5kk) [![PayPal](https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white)](https://www.paypal.me/mnestorov)

## Table of Contents

- [Overview](#overview)
- [Services](#services)
    - [Traefik](#traefik)
    - [Portainer](#portainer)
    - [MariaDB](#mariadb)
    - [phpMyAdmin](#phpmyadmin)
    - [Adminer](#adminer)
    - [Mailhog](#mailhog)
- [Installation](#installation)
    - [Docker Engine](#docker-engine)
- [Manage Docker as a Non-root User](#manage-docker-as-a-non-root-user)
- [Install Mkcert](#install-mkcert)
- [Accessible URLs](#accessible-urls)
- [Portainer Login Credentials](#portainer-login-credentials)
- [Commands](#commands)
    - [Create Network](#create-network)
    - [Build Containers](#build-containers)
    - [Raise Containers](#raise-containers)
    - [Down Containers](#down-containers)
    - [Export Database](#export-database)
    - [Build Container](#build-container)
    - [Start All Containers](#start-all-containers)
    - [Stop All Containers](#stop-all-containers)
    - [Generate New SSL for Container](#generate-new-ssl-for-container)
- [SSL Troubleshooting](#ssl-troubleshooting)
- [References](#references)
- [License](#license)

## Overview

**Initial release by:** [Steliyan Stoyanov](https://github.com/SteliyanPStoyanov)

This Docker development environment provides a comprehensive set of services for web development, including Traefik, Portainer, MariaDB, phpMyAdmin, Adminer, and Mailhog.

## Services

### Traefik
- Version: v2.9
- Function: Reverse proxy and load balancer with automatic SSL.
- Ports: 80 (HTTP), 443 (HTTPS), 8080, 9900, 9901
- Custom Labels: API entrypoints, host rules, TLS configurations.
- Network: Custom defined subnet 172.19.0.0/16.

### Portainer
- Version: Latest
- Function: Web UI for container management.
- Labels: Traefik enabled, host rule, TLS options.
- Network: 172.19.0.3.

### MariaDB
- Version: 10.4.13
- Function: Database server.
- Environment: Root and user credentials setup.
- Labels: Traefik enabled, host rule, TLS options.
- Network: 172.19.0.4.

### phpMyAdmin
- Version: Custom build
- Function: Database administration tool.
- Dependencies: Depends on db service.
- Labels: Traefik enabled, host rule, TLS options.
- Network: 172.19.0.5.

### Adminer
- Version: Latest
- Function: Database management tool.
- Dependencies: Depends on db service.
-Labels: Traefik enabled, host rule, TLS options.
- Network: 172.19.0.6.

### Mailhog
- Version: Latest
- Function: Email testing tool for developers.
-Labels: Traefik enabled, host rule, TLS options.
- Network: 172.19.0.7.

## Installation

### Docker Engine

Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```bash
sudo apt-get update
```

```bash
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

Add Dockers official GPG key:

```bash
sudo mkdir -p /etc/apt/keyrings
```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Use the following command to set up the repository:

```bash
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

To install the latest version, run:

```bash
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Verify that the Docker Engine installation is successful by running the hello-world image:

```bash
 sudo docker run hello-world
```

## Manage Docker as a non-root user

Create the docker group:

```bash
 sudo groupadd docker
 ```

Add your user to the docker group:

```bash
sudo usermod -aG docker $USER
```

You need to log out and log back in so that your group membership is re-evaluated. If you’re running Linux in a virtual machine, it may be necessary to restart the virtual machine for changes to take effect. 

You can also run the following command to activate the changes to groups:

```bash
newgrp docker
```

Verify that you can run docker commands without sudo:

```bash
docker run hello-world
```

This command downloads a test image and runs it in a container. When the container runs, it prints a message and exits.

If you initially ran Docker CLI commands using sudo before adding your user to the docker group, you may see the following error:

**WARNING** 

Error loading config file: `/home/user/.docker/config.json` - `stat /home/user/.docker/config.json: permission denied`

This error indicates that the permission settings for the `~/.docker/` directory are incorrect, due to having used the sudo command earlier.

To fix this problem, either remove the `~/.docker/` directory (it’s recreated automatically, but any custom settings are lost), or change its ownership and permissions.

Change ownership and permissions:

```bash
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```

## Install Mkcert

Before installing the Mkcert utility, you will need to install the required packages to your server. 

You can install it with the following command:

```bash
sudo apt-get install wget libnss3-tools
```

Once all the packages are installed, download the latest version of Mkcert from Github:

```bash
wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-amd64
```

After downloading Mkcert, move the downloaded binary to the system path:

```bash
sudo mv mkcert-v1.4.3-linux-amd64 /usr/bin/mkcert
```

Set the execution permission to the mkcert:

```bash
sudo chmod +x /usr/bin/mkcert
```

Verify the Mkcert version with the following command:

```bash
mkcert --version
```

The command below generate ssl certificate for local development, because this is required for default containers:

```bash 
mkcert -cert-file ./docker_files/cert -key-file ./docker_files/cert-key portainer.localhost traefik.localhost mailhog.localhost elasticsearch.localhost mariadb.localhost phpmyadmin.localhost kibana.localhost
```

## Accessible URLs

- Traefik dashboard: https://traefik.localhost
- Portainer: https://portainer.localhost
- phpMyAdmin: https://phpmyadmin.localhost
- Adminer: https://adminer.localhost
- Mailhog: https://mailhog.localhost
- Elasticsearch: https://elasticsearch.localhost
- MariaDB: https://mariadb.localhost

### Portainer Login Credentials

- **User:** mnestorov
- **Password:** Eu7c16uevCRWkccZ

## Commands

### Create Network

You need to create the traefik-local network:

```bash
docker network create traefik-local
```

### Build Containers

```bash
docker compose build
```

### Raise Containers

```bash
docker compose up -d
```

### Down Containers

```bash
docker compose down
```

### Export Database

Export database from mysql container:

```bash
sudo /usr/bin/mysqldump --complete-insert --all-databases --result-file=/home/sstoyanov/Database/database_name_10_10.sql --skip-add-drop-table --skip-lock-tables --skip-add-locks --user=root --password=root --host=172.20.0.6 --port=3306
```

### Build container

```bash
bin/build
```

### Start all containers

```bash
bin/start
```

### Stop all containers

```bash
bin/stop
```

### Generate new SSL for container

```bash
bin/add_cert_to_traefik
```

## SSL Troubleshooting

### Resolving the Permission Issue

**Change Ownership to Your User**

To run Docker as a non-root user, change the ownership of certificate files to your user.

```bash
sudo chown your_username:your_username ./docker_files/cert ./docker_files/cert-key
```

### Adjust Permissions for the Key File

Set permissions for the key file so that only the owner can read and write.

```bash
chmod 600 ./docker_files/cert-key
```
### Verify the Changes

Check permissions and ownership.

```bash
ls -l ./docker_files/cert ./docker_files/cert-key
```

### Restart Docker Compose

Apply the changes by restarting Docker Compose.

```bash
docker compose down
docker compose up -d
```

## References

- [Install Docker Desktop on Ubuntu](https://docs.docker.com/desktop/install/ubuntu/)
- [Install Portainer CE with Docker on Linux](https://docs.portainer.io/start/install-ce/server/docker/linux)
- [Traefik Proxy Documentation](https://doc.traefik.io/traefik/)

---

## License

This project is released under the MIT License.
