+++
title = 'Connection to Github with SSH'
date = 2024-01-21T12:25:29+08:00
draft = false
+++

##### Create a depository on local

```git
git init
```

##### Set username and email

```git
git config --global user.name "luminary"
git config --global user.email "luminary@g.com"
```

##### Generate a new SSH key

```git
ssh-keygen -t ed25519 -C "your_email@example.com"
```

##### Adding the SSH key to the ssh-agent

In a new *admin elevated* PowerShell window, ensure the ssh-agent is running. You can start it manually:

```powershell
# start the ssh-agent in the background
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
```

In a terminal window without elevated permissions, add your SSH private key to the ssh-agent. If you created your key with a different name, or if you are adding an existing key that has a different name, replace *id_ed25519* in the command with the name of your private key file.

```powershell
ssh-add c:\Users\YOU\.ssh\id_ed25519
```

##### Make ssh-agent auto start on Debian

要在 Debian 上实现 `ssh-agent` 的开机自启动，您可以将以下命令添加到您的 `~/.bashrc` 或 `~/.bash_profile` 文件中：

```bash
if [ -z "$SSH_AUTH_SOCK" ] ; then
    eval $(ssh-agent)
fi 
```

这段代码会检查是否已经设置了 `SSH_AUTH_SOCK` 环境变量（即检查 `ssh-agent` 是否已经在当前 shell 中运行），如果没有设置，则启动一个新的 `ssh-agent` 进程。

请注意，这种方法会在每次打开新的终端时启动一个新的 `ssh-agent` 进程。如果您希望 `ssh-agent` 在系统启动时就启动并一直运行，可以考虑将启动 `ssh-agent` 的命令添加到系统的启动脚本中。

##### Adding a new SSH key to Github account

Open your github personal **settings** and choose the **SSH and GPG keys**. Then click the **New SSH key** button, give your key a **title** as you like and paste all the contents of the **id_ed25519.pub** file into the **key** section. Finally click the **Add SSH key** button to complete adding.

##### Test SSH connection

```bash
ssh -T git@github.com
```

 If it works then it would inform you that:

`Hi user! You've successfully authenticated, but GitHub does not provide shell access.`

##### Some helpful commands

```powershell
ssh-keygen -lf file_path     # Show the fingerprint, notation and algorithm of the key file
ssh-add -l     # Show all the fingerprints of the key file that we've add to our ssh agent
ssh-add file_path     #Add the key file to our ssh agent
ssh-agent -s     # Run the ssh-agent
```

If our **git** CLI couldn't pull or push to the remote repository, we could try [this](https://stackoverflow.com/a/69992322/18821757) out. 

##### Postscript

If it still can't connect to github through SSH, create a `~/user/.ssh/config` file if not exists and add the following:

```
Host github.com
 Hostname ssh.github.com
 Port 22
```

check out this [page](https://stackoverflow.com/questions/15589682/ssh-connect-to-host-github-com-port-22-connection-timed-out) for more information.

###### Source

Check the [original](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) out.
