+++
title = 'Cloudflare_tunnel'
date = 2024-02-24T16:45:57+08:00
draft = false
+++

### References

[Zapic's Blog](https://blog.zapic.moe/archives/tutorial-176.html)

[Lxn-Chan](https://lxnchan.cn/cf-tunnel.html)

### Why would I need Cloudflare Tunnel

Using nginx for port forwarding, for example, configuring a `location/path {}` in `/etc/nginx/sites-available/default`, allows access to services on ports other than 80 or 443 via `domain_name/path`. This is suitable for services with only a root path, such as **cppla**'s docker version [serverstatus](https://github.com/cppla/ServerStatus). However, for services with multiple subpaths, this simple method may fail, such as jadenlee2019's [mynodequery](https://hub.docker.com/r/jaydenlee2019/mynodequery). The reason is that when accessing `domain_name/path` for the first time, nginx performs port forwarding, and the mynodequery service returns a 302 response with `location='Dashboard'`, so the local browser will access `domain_name/Dashboard`. However, nginx is not configured to handle `/Dashboard`, so it is eventually handled by `location / {}`. This also means that if the port forwarding rule is placed in `location / {}` instead of `location /path {}`, you can establish contact with the mynodequery service by accessing `domain_name`. But what if `location / {}` has other services that are more important than mynodequery? You can set up a private tunnel for services on any port on the host through a **cloudflare tunnel**, which provides direct access to the service on that port through a specified **sub domain name**.

### Start

#### Install cloudflared

Visit this [page](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/) to download **cloudflared**. For Debian 12, refer to this [document](https://pkg.cloudflare.com/index.html) and the following:

```bash
# Add cloudflare gpg key
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

# Add this repo to your apt repositories
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared buster main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

# install cloudflared
sudo apt-get update && sudo apt-get install cloudflared
```

#### Login your cloudflare account for authentication

First run the following command:

```bash
cloudflared tunnel login
```

And there will be some prompts like this：

```
Please open the following URL and log in with your Cloudflare account:

https://dash.cloudflare.com/argotunnel?callback=https%3A%2F%2Flogin.argotunnel.com%2F1145141919810%3D

Leave cloudflared running to download the cert automatically.
```

Open the url and select a **domain_name** to turn on **cloudflare argo tunnel**.

#### Create a tunnel

Run the following to create a tunnel:

```bash
cloudflared tunnel create <tunnel_name>  # replace tunnel_name with what you want 
```

And some helpful commands:

```bash
# manual for usage
cloudflared tunnel -help  # cloudflared tunnel -h
cloudflared -h
# List all the existing tunnels
cloudflared tunnel list
```

#### Run the tunnel

Run the tunnel 

```bash
# For example, cloudflared tunnel run --url http://localhost:3241 mynodequery
cloudflared tunnel run --url <Source> <tunnel_name/tunnel_id>
# For more usage, check out cloudflared tunnel -h
```

Default configuration file is `~/.cloudflared/config.yml`. Here is an example.

```
tunnel: 6ff42ae2-765d-4adf-8112-31c55c1551ef
credentials-file: /root/.cloudflared/6ff42ae2-765d-4adf-8112-31c55c1551ef.json
protocol: http2
originRequest:
  connectTimeout: 30s
  noTLSVerify: false

ingress:
  - hostname: gitlab.corp.tech  # replace corp.tech with your domain name
    service: http://localhost:80
  - hostname: gitlab-ssh.corp.tech  # replace corp.tech with your domain name
    service: ssh://localhost:22
  - service: http_status:404
```

If you already complish your configuration file, your could execute the following command to run the tunnel instead.

```bash
cloudflared tunnel run <tunnel_name/tunnel_id> # this would use the default configuration file
```

#### Bind your tunnel with a domain name

Run the following to bind your command with a domain name:

```bash
# For example: cloudflared tunnel route dns mynodequery mynodequery.domain_name
cloudflared tunnel route dns <tunnel_name/tunnel_id> <sub domain_name>
```

(Optional) After this, login in your cloudflare account (https://dash.cloudflare.com/xxx/domain_name/dns/records) and then you would find there is a new **CNAME** record that binds your **sub domian name** with your **tunnel_id specified url**.

#### Visit your service through domain name

For example: **mynodequery.domain_name**

#### Make it a  systemd service (optinal)

Create and edit `/etc/systemd/system/cloudflared.service`

```
[Unit]
Description=Cloudflare Tunnel
After=network.target

[Service]
ExecStart=cloudflared tunnel run --url <Source> <tunnel_name/tunnel_id>
Restart=always
RestartSec=5
StartLimitInterval=3

[Install]
WantedBy=multi-user.target
```

Make it auto start after boot

```bash
systemctl daemon-reload
systemctl enable cloudflared.service
```

Start the cloudflared service

```bash
systemctl start cloudflared.service
```
