+++
title = 'Mynodequery'
date = 2024-02-29T20:49:26+08:00
draft = false
+++

### Source

[Refered blog link](https://blog.captainz.cc/posts/docker_compose_mynodequery.html)

[Project](https://hub.docker.com/r/jaydenlee2019/mynodequery)

### Start

##### install docker and docker-compose

```bash
# install docker
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh && docker --version && systemctl start docker && systemctl enable docker && systemctl restart docker

# install docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose && docker-compose --version
```

##### Make some configuration files

```bash
mkdir ~/MyNodeQuery && cd ~/MyNodeQuery
touch docker-compose.yml
touch appsettings.json
```

Add the following to `docker-compose.yml`

```yaml
version: '3.3'
services:
  mynodequery:
    container_name: mynodequery
    ports:
      - '5000:5000'
    volumes:
      - './appsettings.json:/app/appsettings.json'
    image: 'jaydenlee2019/mynodequery:latest'

  mynodequerydb:
    image: 'docker.io/mysql:latest' # image
    restart: always
    container_name: mynodequery-mysql  #container name
    command: mysqld --default-authentication-plugin=mysql_native_password
    volumes:
      - ./data:/var/lib/mysql  #mounted directory, for permenent store
    # ports:
    #   - '3306:3306'
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: "mynodequery"   #set the password for root
```

Add the following to `appsettings.json`

```json
{
  "Logging": {
      "LogLevel": {
          "Default": "Information",
          "Microsoft": "Warning",
          "Microsoft.Hosting.Lifetime": "Information"
      }
  },
  "MySql": {
      "ConnectionString": ""
  },
  "AllowedHosts": "*",
  "Installed": "false",
  "ReadNodeIpHeaderKey": "X-Real-IP"
 }
```

##### Run the program

Execute the following command to run the project in docker

```bash
docker-compose up -d
```

Create a new database of Mysql in the docker container

```bash
# enter the container of mysql
docker exec -it mynodequery-mysql /bin/bash
# log in mysql as root
mysql -uroot -p #then input the password for root (it's "mynodequery")
# create a new database
create database mynodequery charset=utf8;
# quit from mysql
exit
# quit from mysql container
exit
```

##### Visit the frontend to finish other configuration

Visit `http://ip:5000` in our browser.

##### Attention

Modify the `sh` to `bash` in the command which is generated when a node added.
