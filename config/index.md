# Some configurations


### Go's configuration in VScode

**Problem**: Could not download VScode plugins of Go due to a famous firewall(I guess). here is the [reference]([一招完美解决vscode安装go插件失败问题_installing github.com/cweill/gotests/gotests@v1.6.-CSDN博客](https://blog.csdn.net/qq_41065919/article/details/107710144)).

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

<img src="file:///C:/Users/荣耀/AppData/Roaming/marktext/images/2024-01-04-15-01-26-image.png" title="" alt="" width="455">

### Install Mysql and modify password for user root

Update the package tool:

```shell
sudo apt update
```

Download and install Mysql([reference]([Ubuntu（WSL2）安装MySQL数据库 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/550946252)))

```shell
sudo apt install mysql-server -y
```

To update the user **root**'s password in mysql, switch to **root** in Linux ([reference]([Ubuntu 20.04首次安装Mysql登录密码问题_ubuntu 启动mysql服务要输入密码-CSDN博客](https://blog.csdn.net/james_8/article/details/113375068)))

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

Alter the password of root in Mysql as you like (in this example, the new password is 'love')

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

