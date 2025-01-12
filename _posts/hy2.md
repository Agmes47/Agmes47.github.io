+++
title = 'hy2'
date = 2024-01-20T16:50:56+08:00
draft = false
+++

###### Related links

V2rayN: <https://github.com/2dust/v2rayN/releases/latest>

Hysteria 2 project：<https://github.com/apernet/hysteria/releases>

Hysteria 2 document：<https://v2.hysteria.network/zh/>

##### Install hysteria 2

```bash
bash <(curl -fsSL https://get.hy2.sh/)
```

##### Generate Self-Signed Certification

```bash
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout /etc/hysteria/server.key -out /etc/hysteria/server.crt -subj "/CN=bing.com" -days 36500 && sudo chown hysteria /etc/hysteria/server.key && sudo chown hysteria /etc/hysteria/server.crt
```

###### All the commands might need

```bash
#start Hysteria2
systemctl start hysteria-server.service
#restart Hysteria2
systemctl restart hysteria-server.service
#status of Hysteria2
systemctl status hysteria-server.service
#stop Hysteria2
systemctl stop hysteria-server.service
#enable auto start after booting
systemctl enable hysteria-server.service
#check the logs
journalctl -u hysteria-server.service
```

##### The configuration of server

```bash
cat << EOF > /etc/hysteria/config.yaml
listen: :443 #port

#use CA certificate
#acme:
#  domains:
#    - a.com #Your domain that has been resolved
#  email: test@sharklasers.com

#or use self-signed certificate
#tls:
#  cert: /etc/hysteria/server.crt
#  key: /etc/hysteria/server.key

auth:
  type: password
  password: 123456    #Custom your password

masquerade:
  type: proxy
  proxy:
    url: https://bing.com     #Replace it with what you want
    rewriteHost: true
EOF
```

##### The configuration of client

```bash
server: [ip]:443
auth: 123456

bandwidth:
  up: 20 mbps
  down: 100 mbps

tls:
  sni: a.com
  insecure: false #set to 'true' when using self-signed certificated

socks5:
  listen: 127.0.0.1:1080
http:
  listen: 127.0.0.1:8080
```

Source: [reference](https://bulianglin.com/archives/hysteria2.html)
