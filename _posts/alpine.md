+++
title = 'Alpine'
date = 2024-02-28T22:29:04+08:00
draft = false
+++

### Basic

Allow net-working:

```ash
rc-update add networking
service networking start
```

### Scripts

source

```bash
#Warp：
apk update && apk add curl && apk add bash && apk add wget
wget -N https://gitlab.com/fscarmen/warp/-/raw/main/menu.sh && bash menu.sh 4

#nezha：
apk add bash curl <url>

#X-UI：
apk add curl&&apk add bash && bash <(curl -Ls https://raw.githubusercontent.com/Lynn-Becky/Alpine-x-ui/main/alpine-xui.sh)

#XrayR：
apk add wget sudo curl && wget -N https://github.com/Cd1s/alpineXrayR/releases/download/one-click/install-xrayr.sh && chmod +x install-xrayr.sh && bash install-xrayr.sh

#ArgoX：
bash <(wget -qO- https://raw.githubusercontent.com/fscarmen/argox/main/argox.sh)

#SING-BOX：
bash <(wget -qO- https://raw.githubusercontent.com/fscarmen/sing-box/main/sing-box.sh)

#Alpine package management
rc-service [package_name] restart
```

### FAQ

###### Coulld not ban SSH password log in?

[reference](https://superuser.com/a/1816612)
Execute 

```bash
sshd -T | grep passwordauthentication     
```

if you see output `passwordauthentication yes`, some configurations are set prior to default /etc/ssh/sshd_config, they are located in `/etc/ssh/sshd_config.d/` , you can search `passwordauthentication` from them or simply remove them by

```bash
rm /etc/ssh/sshd_config.d/*
```

##### Apline 的库

Alpine Linux 使用的是 `musl` libc 而不是大多数 Linux 分发版使用的 `glibc`。因此，在为 Alpine Linux 构建静态可执行文件时，需要确保你的应用链接到 `musl` 而不是 `glibc`。

你的 `Makefile` 中的 `CGO_ENABLED=1` 表明你可能需要 CGO 来编译 C 代码。如果你的 Go 应用依赖于 C 库，并且这些库在 Alpine Linux 上存在或者可以被 `musl` 正确处理，可能需要进行一些调整。

这里有一个在 Alpine Linux 上构建 Go 应用的示例：

Makefile

```makefile
build:
    @CGO_ENABLED=0 GOOS=linux go build -a -tags netgo -ldflags '-w -extldflags "-static"' -o bin/servi .

run: build
    ./bin/servi
```

这里，我们设置 `CGO_ENABLED=0` 来禁用 CGO，这通常是创建一个完全静态链接的可执行文件的推荐方式。我们也添加了 `-tags netgo` 和一些 `ldflags` 来确保所有的网络相关的代码使用纯 Go 的实现，并且所有的 C 代码都被静态链接。

如果你的程序确实需要 CGO，并且需要链接到 C 库，那么你可能需要安装 `musl` 的交叉编译工具链，并且在构建时指定它。这通常是一个更高级的主题，可能需要根据你的具体需求调整构建过程。

请根据你的项目的具体需求来决定是否需要对 `Makefile` 进行这样的修改。如果你确实需要进行修改，还需要确保你的项目依赖项都能够在 Alpine Linux 上正确运行。
