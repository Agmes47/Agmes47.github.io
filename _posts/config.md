+++
title = 'Some configurations'
date = 2010-01-02T16:49:08+08:00
draft = false
tags = ["configurations"]
+++

### Go's configuration in VScode

[Setting up Go in Windows](https://learn.microsoft.com/zh-cn/azure/developer/go/configure-visual-studio-code)

**Problem**: Could not download VScode plugins of Go due to a famous firewall(I guess). here is the [reference](https://blog.csdn.net/qq_41065919/article/details/107710144).

Solvation: 

This is the old version(abandoned): 

```powershell
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.io,direct
```

Open cmd or powershell and run the following commands:

```powershell
go env -w GO111MODULE=on
go env -w GOPROXY=https://proxy.golang.com.cn,direct
```

Then restart VScode and install the Go plugins again, you should succeed.

### Modify Powershell's default lauch size in Window11

Can't believe these old ways and nonsense on the Internet even though it's pretty easy. Open Powershell and then right click the window bar and go into **settings**. Scroll down and find out the **Lauch size**

### Install Mysql and modify password for user root

Update the package tool:

```shell
sudo apt update
```

Download and install Mysql([reference](https://zhuanlan.zhihu.com/p/550946252))

```shell
sudo apt install mysql-server -y
```

To update the user **root**'s password in mysql, switch to **root** in Linux ([reference](https://blog.csdn.net/james_8/article/details/113375068))

```shell
su root
```

Use your Linux **root** password to login mysql as **root**

```shell
mysql -u root -p
```

Ensure the way of login as **root** with password never expires

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
```

Use the mysql_native_password plugin and alter the password of root in Mysql as you like (in this example, the new password is 'love'). For more about the plugin, check in [reference](https://dev.mysql.com/doc/refman/8.0/en/sha256-pluggable-authentication.html).

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'love'
```

Refreshing to ensure the new priviledges

```sql
FLUSH PRIVILEDGES
```

Then you can use your new password to login with the following command

```shell
sudo mysql -u root -p
```

For more knowledge, a brief [introduction](https://zhuanlan.zhihu.com/p/146502753) to the C/S framework of Mysql. 

###### Go-sql-driver

If you're learning about  Go accessing a relational database [tutorial](https://go.dev/doc/tutorial/database-access), you should add the  following code after the initiation of `cfg`. For more information, please check on [page](https://github.com/go-sql-driver/mysql/issues/625).

```go
cfg.Params = map[string]string{"allowNativePasswords": "true"}
```

### Conflict of <u>blcok visual mode</u> and <u>paste</u> using vim in WSL

Open a windows terminal, and press `ctrl + shift + ,` to open `settings.json`. Then comment the following line:

```json
  "actions": [
    ...
    // { "command": {"action": "paste", ...}, "keys": "ctrl+v" }, <------ THIS LINE
```

After doing this, you can switch to visual block mode as usual and paste with `CTRL + SHIFT + v`. For more information, please check out the [reference](https://stackoverflow.com/questions/61824177/visual-block-mode-not-working-in-vim-with-c-v-on-wslwindows-10).

###### Anecdote

Before I found the kludge above, I've tried to modify the `~/.vimrc`. In my case, I wan't have such a file in my user path `~/` initially. So I created one and add `map <C+K> <C+V>` into the it and then run `source ~/.vimrc` which resulted in error: <u>-bash: /home/user_name/.vimrc: line 1: syntax error near unexpected token `<'
-bash: /home/user_name/.vimrc: line 1: `map <C+K> <C+V>'</u>. So I give up this way and just deleted the line I've added in and preserve the `~/.vimrc`. After the problem was solved, I continued learning Go using **vi** and suddenly found the auto indentation with which I felt comfortable failed. This was way more painful than the conflict between  paste and block vision and I was anxiously to it. Firstly I thought it was dued to the modification I had done in windows terminal's `settings.json` file and I found the original version online and recovered it(Next time if I'm going to modify such kind of file I should copy a backup before implementing any modification.). But it doesn't help. And then I tried to add `set autoindent` into `~/.vimrc`. It kind of worked but not as the same as what I've been used to.(Seems like a fixed but not flexible indentation as the original one. I mean, the orignal one knows I writting Go codes.) After hours of searching and thinking, I thought maybe `~/.vimrc` is the root cause since it was the first thing I had tried to add to relative configuration. So I just simply deleted it and it worked. Evething was back. I guessed `~/.vimrc` would take over the configuraion of **vi** or **vim** even it's empty which means do nothing that lead to the disfunction of auto indentaion. Go's own rules would then prevail after the deletion of `~/.vimrc`.

### Choco and make

[Source](https://developer.aliyun.com/article/1212970)

Operating system: **windows 11**

Terminal: **Powershell**

###### Install Chocolatey

Open powershell as administrator and execute the following

```powershell
iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
```

###### Using Chocolatey to install make

You may need to reopen powershell as administrator after installing Chocolatey and run the following 

```powershell
choco install make
```

### Message forwarding bot in Telegram

1. Ask **Botfather** for a **token**

2. Ask **Livegram Bot** to create our bot withen the token

3. Our bot chatting url is **t.me/bot_name**

**Attention**:  The owner of the bot need to reply the message someone else sending to the bot so that the none-bot-owner can view the message from the bot owner. For example, let's  assume Alice is the owner of the bot. When Bob sends some messages to Alice's bot, Alice should reply to one of the messages from Bob using right click so that Bob could see Alice's replying.

### Openssh for windows

[link](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell)


#### Problems within the installation and configuration of Neovim on WSL2 Ubuntu
**Problem 1**: Keyword(like `vim.keymap`) indentification failed.
**Solution**:
Just install unstable neovim version:
```fish
sudo add-apt-repository ppa:neovim-ppa/unstable
sudo apt-get update
sudo apt-get install neovim
```
[reference](https://stackoverflow.com/a/76199901)
**Problem 2**: Unable to download Lazy vim.
**Solution**:
autocrlf should be set to false not true. This is not a LazyVim bug. This is a problem with Neovim itself. Just don't set this git global to true. Or set it to false temporarily to download and install LazyVim and then set it back to true again. But whenever you want to install or update plugins it should be set to false. The issue that I linked has detailed information about this problem.
PS: Set autocrlf to false, delete ~/.local/share/nvim and restart Neovim to install everything from scratch.
```fish
git config --global core.autocrlf false
```
[reference](https://github.com/LazyVim/LazyVim/issues/3072)
#### Different syntax in fish from bash
```fish
# fish
eval (ssh-agent -c)
```
```bash
# bash
eval $(ssh-agent -s)
```

### Setting up global dark mode in Arch Linux

Edit the `hyprland.config`, adding:(the path of the installed themes: `/usr/share/themes/`)

    exec-once = gsettings set org.gnome.desktop.interface gtk-theme "YOUR_DARK_GTK3_THEME"   # for GTK3 apps
    # exec-once = gsettings set org.gnome.desktop.interface color-scheme "prefer-dark"   # for GTK4 apps
    # env = QT_QPA_PLATFORMTHEME,qt6ct   # for Qt apps
    
[Reference](https://github.com/hyprwm/Hyprland/discussions/5867)

### The path where **wofi** pull the applications launching infomation from

    /usr/share/     # I think it might be /usr/share/applications/, because there are a lot .desktop files.
    ~/.local/share/

In the event $XDG_DATA_HOME is not specified it defaults to ~/.local/share. If $XDG_DATA_DIRS is not specified it defaults to /usr/local/share:/usr/share.   ~/.local/share/applications
[reference](https://bbs.archlinux.org/viewtopic.php?id=296070)

### Prefer dark theme setting path

modify `~/.config/gtk-3.0/setting.ini` to  

    gtk-application-prefer-dark-theme=false    


### Hugo config

[reference](https://discourse.gohugo.io/t/page-hugo-is-deprecated-page-rsslink-is-deprecated/27037)


#### Can't find the **screen capture** source in OBS studio

Maybe it can be solved via installing **xdg-desktop-portal-wlr** package
