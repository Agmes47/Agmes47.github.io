+++
title = 'Smokeping'
date = 2024-03-01T16:04:57+08:00
draft = false
+++

### Source

[zzz's blog](https://blog.captainz.cc/posts/smokeping_install.html)

### Start

###### Install docker and enable it.

```bash
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh && docker --version && systemctl start docker && systemctl enable docker && systemctl restart
```

###### Create a directory and open a port

```bash
myserve="smokeping"
sudo ufw allow 8899/tcp comment $myserve && sudo ufw reload
mkdir -p ~/myserve/$myserve && cd ~/myserve/$myserve
```

###### Create a configuration file

```bash
cat > docker-compose.yml << EOF
version: "2.1"
services:
  smokeping:
    image: lscr.io/linuxserver/smokeping:latest
    container_name: smokeping
    restart: unless-stopped
    environment:
      - PUID=root   # Customized, user name or UID.
      - PGID=root   # Customized, group name or GID.
      - TZ=Asia/Shanghai
    volumes:
      - ./config:/config
      - ./data:/data
    ports:
      - 8899:80
EOF
```

###### Pull down the image of the project

```bash
docker compose pull
```

###### Run the project in docker

```bash
docker compose up -d
```

#### Custom

###### The name of the node of the host machine

Modify `config/General`

```
*** General ***

owner    = LinuxServer.io
contact  = ironicbadger@linuxserver.io
mailhost = my.mail.host
cgiurl   = http://localhost/smokeping.cgi
syslogfacility = local0

display_name = machine_name   # here
@include /config/pathnames
```

###### Probing frequency

Modify `config/Database`

```
*** Database ***
step     = 180     # Interval of every two adjacent probes
pings    = 20      # Probing times of each probe
```

###### Probe types

Modify `config/Probe`

```
*** Probes ***

+ FPing
binary = /usr/sbin/fping
packetsize = 1000

+ FPing6
binary = /bin/ping6   # Can't work fine.

+ DNS
binary = /usr/bin/dig
lookup = baidu.com
pings = 5             # this 'pings' would cover the one in config/Database
step = 300            #this 'step' would cover the one in config/Database

+ Curl
binary = /usr/bin/curl
forks = 5
offset = 50%
```

###### Probing targets

Modify `config/Targets` according to this [page](https://raw.githubusercontent.com/AhFeil/bash-script/main/smokeping-Targets).

##### Restart the docker container to apply new configuration

```bash
docker compose down
docker compose up -d
```
