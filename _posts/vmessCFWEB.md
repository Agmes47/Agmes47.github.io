+++
title = 'Vmess + CF + WEB'
date = 2024-01-22T23:50:01+08:00
draft = false
+++

#### Prerequisite

A vps running x-ui, a domain utilizing Cloudflare for DNS resolution and CDN services.

#### Step 1

In x-ui, add an inbound. 

```
protocol: vmess
listening address: 127.0.0.1
port: random
#----------
#add a user
#----------
network: ws
path: /example #replace 'example' with something you can memorize
```

#### Step 2

Copy the configuration link and import into v2ray. And make the following modification.

```
address: *.*.*.*     #the IP of your vps
port: 443         #80 if not going to use https protocol
tls: tls
```

#### Step 3

Modify the configuration of nginx(default path: `/etc/nginx/sites-available/default`)

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                proxy_pass https://www.bing.com;
                proxy_ssl_server_name on;
                proxy_redirect off;
                sub_filter_once off;
                sub_filter "www.bing.com" $server_name;
                proxy_set_header Host "www.bing.com";
                proxy_set_header Referer $http_referer;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header User-Agent $http_user_agent;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
                proxy_set_header Accept-Encoding "";
                proxy_set_header Accept-Language "zh-CN";
        }
        location /example {                        #replace the path
                proxy_redirect off;
                proxy_pass http://127.0.0.1:52321;     #replace the port num
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }
```

Reference: <https://bulianglin.com/archives/guide.html>

##### Journey

Installed nginx, avoiding confliction with hysteria which is listening on port 443. (No problem, nginx can start fluently.)

Modified the configuration of nginx within the help of chatgpt. (Too simple if not telling it  that I was using websocket protocol. And I was missing the `proxy_set_header Connection "upgrade";` part for such a long time, which made nginx reture 400 to the client.)

Clearified the websocket path and user id in x-ui. (Made a xray proccess listen on a port and its ws path should be the same with the path of client v2ray node and nginx passing rules path. One is for authentification and anther is for redirecting. User id is also for authentification.) 
