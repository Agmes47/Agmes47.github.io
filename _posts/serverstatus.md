+++
title = 'Serverstatus'
date = 2024-02-21T18:39:50+08:00
draft = false
+++

### Build a server monitor

##### Source

Project: [serverstatus by cppla](https://github.com/cppla/ServerStatus)

I personally mainly referenced this blog [post](https://blog.tanglu.me/server-status/)

##### Server

Install **docker** and **docker compose**

```bash
curl -fsSL https://get.docker.com -o get-docker.sh && sh ./get-docker.sh
```

Check if **docker** successfully installeddocker compose version

```bash
docker -v
# Some like 'Docker version 23.0.2, build 569dd73'
docker compose version
# Some like 'Docker Compose version v2.17.2'
```

Download two configuration files: `serverstatus-config.json` and `serverstatus-monthtraffic` . Modify them according to your situation.

```bash
wget --no-check-certificate -qO ~/serverstatus-config.json https://raw.githubusercontent.com/cppla/ServerStatus/master/server/config.json && mkdir ~/serverstatus-monthtraffic
```

Run the docker container

```bash
# For the 'port_1:port_2', the left one is of the host machine and the right one is of the container
docker run -d --restart=always --name=serverstatus -v ~/serverstatus-config.json:/ServerStatus/server/config.json -v ~/serverstatus-monthtraffic:/usr/share/nginx/html/json -p 80:80 -p 35601:35601 cppla/serverstatus:latest
```

Now you can have a look on the frontend page by visit `http://localhost:port` 

###### Use nginx as reverse proxy

Edit `/etc/nginx/sites-available/default` 

```
location /status {
                proxy_redirect off;
                proxy_pass http://127.0.0.1:8080/; # don't forget the '/' in the end
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }
```

Reload nginx configuration

```bash
systemctl reload nginx
```

Now you could visit it through `ip/status/` or `domainName/status/`

##### Client

Install the agent file `client-linux.py`

```bash
wget --no-check-certificate -qO client-linux.py 'https://raw.githubusercontent.com/cppla/ServerStatus/master/clients/client-linux.py'
```

Modify it according to your needs:

```python
SERVER = "1.1.1.1"    # IP of server
USER = "s01"            # username, corresponding to the username of server side


PASSWORD = "USER_DEFAULT_PASSWORD"        # password, corresponding to the password of server side
PORT = 45601                            # client port
CU = "mall.10010.com"                    # recommanded tcping address
CT = "www.189.cn"                        # recommanded tcping address
CM = "www.bj.10086.cn"                    # recommanded tcping address
PROBEPORT = 80                            #
PROBE_PROTOCOL_PREFER = "ipv4"  # ipv4, ipv6 preference when ping cu ct cm
PING_PACKET_HISTORY_LEN = 100            
INTERVAL = 1      
```

Install **python3**

```bash
# check if python3 installed
python3 --version
# if not, install it
apt install python3
```

Make it a **daemon**

```
# Add a daemon /etc/systemd/system/tzstatus.service
# replace'ExecStart' with your needed command

#vi /etc/systemd/system/tzstatus.service

[Unit]
Description=serverstatus
After=network.target

[Service]
ExecStart=python3 /root/client-linux.py
Restart=always

[Install]
WantedBy=multi-user.target
```
