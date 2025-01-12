+++
title = 'Naive'
date = 2024-01-27T13:45:31+08:00
draft = false
+++

## Server

### Update and upgrade

```bash
apt update && apt upgrade
```

### Prerequisite

1. Make sure the memory of your machine is larger than 1 GB. You may try **swap** if not.

2. A domain name that has been resolved(**no CND**).

#### Network tools for downloading

```bash
apt-get install socat
```

#### Installing Go environment

```bash
wget https://go.dev/dl/go1.21.1.linux-amd64.tar.gz    #replace 'go1.21.1.linux-amd64.tar.gz' with the new go version
tar -C /usr/local -xzf go1.21.1.linux-amd64.tar.gz    #replace 'go1.21.1.linux-amd64.tar.gz' with the new go version
export PATH="/usr/local/go/bin:$PATH"    #temporary environment variable
go env -w GO111MODULE=on    #Set the Go module model as default behavior
rm -f go1.21.1.linux-amd64.tar.gz    #you may want to delete it
```

#### Installing **caddy** within **naive** plugin

```bash
cd && go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest   
~/go/bin/xcaddy build --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive     #buuild caddy with xcaddy
rm -rf go    #you may want to delete it
```

#### Running caddy as a daemon process

Executilize caddy and move it to `/usr/bin/`

```bash
chmod a+x caddy
mv caddy /usr/bin/
```

Create a directory

```bash
mkdir /etc/caddy
```

Create user and group

```bash
groupadd --system caddy
useradd --system \
    --gid caddy \
    --create-home \
    --home-dir /var/lib/caddy \
    --shell /usr/sbin/nologin \
    --comment "Caddy web server" \
    caddy
```

Create `caddy.service` in `/etc/systemd/system/`

```bash
vi /etc/systemd/system/caddy.service
```

And paste the following

```
[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target
[Service]
User=caddy
Group=caddy
ExecStart=
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE
[Install]
WantedBy=multi-user.target
```

Configurations for caddy

```bash
nano /etc/caddy/Caddyfile
```

Paste the following

```
:443, example.domain.com #domain name
tls example@example.com #email
route {
 forward_proxy {
   basic_auth user pass #user and password
   hide_ip
   hide_via
   probe_resistance
  }
 #multi-user supported
 forward_proxy {
   basic_auth user2 pass2 #user and password
   hide_ip
   hide_via
   probe_resistance
  }
 reverse_proxy https://www.baidu.com {
        header_up Host {http.reverse_proxy.upstream.hostport}
        header_up  X-Forwarded-Host  {host}
    }
}
```

Format Caddyfile 

```bash
caddy fmt --overwrite /etc/caddy/Caddyfile
```

Apply for a certification

```bash
curl https://get.acme.sh | sh
ln -s /root/.acme.sh/acme.sh /usr/local/bin/acme.sh
acme.sh --set-default-ca --server letsencrypt
#substitute 'example.domain.com' with uour domain
#attention：add '--listen-v6' for some ipv6 hosts like hax.co.id/ for the following commands
acme.sh --issue -d example.domain.com --keylength ec-256 --standalone --insecure --force
acme.sh --install-cert -d example.domain.com --ecc \
        --key-file       /etc/caddy/example.domain.com.key  \
        --fullchain-file /etc/caddy/example.domain.com.pem
```

#### Start caddy

```bash
chown -R caddy:caddy /etc/caddy/
systemctl daemon-reload
systemctl enable caddy
systemctl start caddy
#check out current status
systemctl status caddy
#reload caddy once modified Caddyfile
systemctl reload caddy
```

## Client

#### Download and configuration

Client [Download](https://github.com/klzgrad/naiveproxy/releases/latest) 

`config.json` 

```json
Bash
{
  "listen": "socks://127.0.0.1:1080",
  "proxy": "https://user:password@example.domain.com"
}
```

#### Apply V2rayn

Please check it out in the sources.

Sources:

[1](https://www.kkiikk.top/index.php/archives/211/)

[2](https://bulianglin.com/archives/naive.html)
