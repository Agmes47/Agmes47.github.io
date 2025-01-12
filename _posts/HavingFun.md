---
title: "Having Fun"
authors: [Aclius]
date: 2000-01-01T00:00:00+08:00
---

## How was this blog builded

[source](https://cuttontail.blog/blog/create-a-wesite-using-github-pages-and-hugo/) 

[theme](https://themes.gohugo.io/themes/manis-hugo-theme/) 

## Random

###### Keyboard got locked

A cat standing on my keyboard for a good amount of seconds and the keyboard got locked afer instant sharp 'di' sound. The solution is press the right `Shift` key for more than 8 seconds. Reference: <https://www.zhihu.com/question/539981785#>

## VPS

#### Basic

###### Add a new user

`adduser` and `useradd` are different. Use `adduser`. So applied to other similar commands, I guess.

###### Add a pubkey file

First generate a pair of **publicKey **and **secretKey** files:

```powershell
#Windows
ssh-keygen -t rsa -b 4096
```

Then upload the pubkey file to `~/`, and then put it in the correct position as following:

```bash
$ cd ~ && mkdir .ssh
$ mv pubkey_file .ssh/authorized_keys
```

###### Modify SSH configuration

In `/etc/ssh/sshd_config`

```
Port 22     # replace it with a high number port
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes 
```

###### Add a path to the shell

in `~/.bashrc`, add the following:

```
export PATH=$PATH:/usr/sbin
```

###### Set up firewall

```bash
$ sudo apt install ufw
$ sudo ufw enable
```

###### Swap

Abort the previous one if necessary

```bash
swapoff swapfile     # Assumed we're in the directory of 'swapfile'
rm swapfile
```

Create a directory `/swap` for the new one 

```bash
cd / && mkdir swap && cd swap
```

Create a `swapfile` 

```bash
dd if=/dev/zero of=swapfile bs=1M count=1024     # adjust 'count' as you wish
```

 Mark it as swap space and activate it

```bash
mkswap swapfile
swapon swapfile
chmod 600 swapfile     # for safety cocnern
```

Check if it's activatted

```bash
swapon --show
```

Set as auto mounting after booting

```bash
echo "/swap/swapfile none swap sw 0 0" >> /etc/fstab
```

###### Set up congestion control algorithm(bbr) and queue scheduling algorithm(fair queuing)

```bash
#check the current algrithm
sysctl net.ipv4.tcp_congestion_control
#check the Linux version (expect to be newer than 4.9)
uname -r

#BBR set up
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

###### Check outbound IP address

```bash
curl -4 ip.sb    # ipv4
curl ip.p3terx.com    #ipv4 and AS
```

###### Timezone

[source](https://zhuanlan.zhihu.com/p/137122724)

Check the current time zone

```bash
timedatectl
```

List all the time zone candidates

```bash
timedatectl list-timezones
```

Set a new timezone

```bash
sudo timedatectl set-timezone Africa/Accra
```

#### Advance

###### For **ipv6** only VPS:

Modify to Google DNS server:

```bash
cp /etc/resolv.conf{,.bak}; echo -e "nameserver 2a00:1098:2b::1\nnameserver 2a01:4f9:c010:3f02::1\nnameserver 2a01:4f8:c2c:123f::1\nnameserver 2a00:1098:2c::1" > /etc/resolv.conf
```

Add an outbound IPv4 Address to an IPv6 VPS via **Warp** 

```bash
wget -N https://gitlab.com/fscarmen/warp/-/raw/main/menu.sh && bash menu.sh 4
```

 **Sing-box** + **argo**

```bash
bash <(wget -qO- https://raw.githubusercontent.com/fscarmen/sba/main/sba.sh)
```

#### Pandora's box

###### Daily automatic check-in in nodeseek by session cookie

Install the [pre-compiled binaris](https://github.com/lwthiker/curl-impersonate#pre-compiled-binaries) (this step is to pass through TLS fingerprint detection.)

```bash
# Ubuntu/Debian
sudo apt install libnss3 nss-plugin-pem ca-certificates
```

Download and unzip to get the executable binaries from [release page](https://github.com/lwthiker/curl-impersonate/releases)

```bash
# Download zip
wget https://github.com/lwthiker/curl-impersonate/releases/download/v0.6.1/curl-impersonate-v0.6.1.x86_64-linux-gnu.tar.gz
# Unzip them to the current directory
tar -zxvf curl-impersonate-v0.6.1.x86_64-linux-gnu.tar.gz
```

Get your session cookie by `F12` -> `Application` -> `Storage/Cookies`

Test a binary

```bash
# Replace xxx with your session cookie
# If success, it will return some JSOM data
curl_chrome116 'https://www.nodeseek.com/api/attendance?random=true' --compressed -X POST -H 'Cookie: session=xxx'
```

(Optional) I personally like to wrap this command into a shell script and then automatically run it uising **cron**.

Edit a `name.sh`:

```bash
#!/bin/bash
/path/to/curl_impersonate/curl_chrome110 'https://www.nodeseek.com/api/attendance?random=true' --compressed -X POST -H 'Cookie: session=xxx'
```

Edit **crontab** by running `crontab -e`

```
# Add the following line
1 0 * * * /path/to/name.sh
# If you want to log the information, consider adding the following(create log.txt firstly)
1 0 * * * /path/to/name.sh 2>&1 | grep -oP '\{[^{}]+\}'  >> /path/to/log.txt
```

So far, it seems like the the expiration of the session cookie is just one month. I may need to update it every month or after changing the login password or login or logout each time.

###### Self-signed certificate

```bash
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout /home/bauk/server.key -out /home/bauk/server.crt -subj "/CN=bing.com" -days 3650 && sudo chown bauk /home/bauk/server.key && sudo chown bauk /home/bauk/server.crt
```

#### Scripts

[link](https://bulianglin.com/archives/nicename.html)

##### X-UI

```bash
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/956bf85bbac978d56c0e319c5fac2d6db7df9564/install.sh) 0.3.4.4
```

##### dd operating system

[Useful source](https://blockxyz.notion.site/DD-VPS-fb837703b3ac4011bb2362e3a56ac148) and [original script source](https://www.moeelf.com/archives/293.html)

```bash
bash <(wget --no-check-certificate -qO- 'https://moeclub.org/attachment/LinuxShell/InstallNET.sh') -d 11 -v 64 -a -p custom_password -port custom_ssh_ports
```

###### Various scripts

[Reference](https://www.nodeseek.com/post-19427-1)

## xray

[configuration tutorial](https://github.com/chika0801/Xray-examples?tab=readme-ov-file)

###### Log of xray

To check the log of xray

```bash
journalctl -u xray -o cat -e # 
journalctl -u xray -o cat -f #
```

By default, what we see after running the above commands are the system service log of xray service. And they are ususally stored in `/var/log/journal/` directory as binary files. By default, these log files could grow larger and larger without any limit. To prevent they taking too much disk space, we could edit `/etc/systemd/journald.conf`. 

```conf
# Limit Log Size: Set a maximum size for individual journal files. For example
SystemMaxUse=1G
# Limit Disk Usage: Set a maximum disk space usage for all journal files combined.  For example:
SystemMaxFileSize=100M
# Enable Time-based Rotation. For example, to keep logs for 1 week, use:
SystemKeepLogs=1week
# Adjust Vacuuming Frequency: systemd provides automatic vacuuming of journal files, which removes older log data to keep within the configured limits. You can adjust the frequency of vacuuming by setting the SystemMaxFileSec option in /etc/systemd/journald.conf. For example, to vacuum journal files every day, use:
SystemMaxFileSec=1day
```

**Triggering Rotation Manually**: You can also manually trigger log rotation using the `journalctl` command. For example, to rotate the journal files immediately, use:

```bash
journalctl --rotate
```

After making changes to the `journald.conf` file, you need to restart the `systemd-journald` service for the changes to take effect:

```bash
sudo systemctl restart systemd-journald
```

## iptables

要禁止或允许特定IP地址对您的机器进行访问，您可以使用iptables。以下是一些基本的iptables命令示例：

1. 禁止特定IP地址访问您的机器：
   
   bash
   
   `sudo iptables -A INPUT -s 192.168.1.100 -j DROP`
   
   这会将IP地址`192.168.1.100`添加到输入链中，并将其动作设置为`DROP`，从而拒绝来自该IP地址的所有流量。

2. 允许特定IP地址访问您的机器：
   
   bash
   
   `sudo iptables -A INPUT -s 192.168.1.100 -j ACCEPT`
   
   这会将IP地址`192.168.1.100`添加到输入链中，并将其动作设置为`ACCEPT`，从而允许来自该IP地址的所有流量。

请注意，这些规则将在系统重启后失效。如果您想要永久性地保存这些规则，您需要将规则保存到iptables规则文件中。您可以使用以下命令保存规则：

bash

`sudo iptables-save > /etc/iptables/rules.v4`

要确保这些规则在系统启动时自动加载，请确保在`/etc/network/interfaces`中包含以下行：

javascript

`pre-up iptables-restore < /etc/iptables/rules.v4`

或者，如果您的系统使用`systemd`，您可以使用以下命令：

bash

`sudo iptables-save > /etc/iptables/rules.v4 sudo systemctl restart iptables`

这将保存规则并重启iptables服务，以便规则在系统启动时自动加载。

## Transfering files between Linux machines

    scp -P port_number file_path user_name@1.1.1.1 destination_path/

## Unix socket and TCP/IP socket

[Q/A on stack overflow](https://serverfault.com/questions/124517/what-is-the-difference-between-unix-sockets-and-tcp-ip-sockets)

A UNIX socket, AKA Unix Domain Socket, is an inter-process communication mechanism that allows bidirectional data exchange between processes running on the same machine.

IP sockets (especially TCP/IP sockets) are a mechanism allowing communication between processes over the network. In some cases, you can use TCP/IP sockets to talk with processes running on the same computer (by using the loopback interface).

UNIX domain sockets know that they’re executing on the same system, so they can avoid some checks and operations (like routing); which makes them faster and lighter than IP sockets. So if you plan to communicate with processes on the same host, this is a better option than IP sockets.

Edit: As per Nils Toedtmann's comment: UNIX domain sockets are subject to file system permissions, while TCP sockets can be controlled only on the packet filter level.
