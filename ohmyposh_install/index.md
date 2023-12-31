# How to use OhMyPosh to decorate your terminal


##### 1. Powershell

At the beginning, I was trying to implement it on WindowsPowershell and result in a mess which I've forgot(For more information about the differences between them, please check out this [page]([Differences between Windows PowerShell 5.1 and PowerShell 7.x - PowerShell | Microsoft Learn](https://learn.microsoft.com/en-us/powershell/scripting/whats-new/differences-from-windows-powershell?view=powershell-7.3))). Then I strictly follow steps on the official [document]([Introduction | Oh My Posh](https://ohmyposh.dev/docs/)) while at  the same time still encounter some problems. Here I will show the proper way I've tried on to complete this task.

###### (1) Install OhMyPosh

Open Powershell and execute the command:

```powershell
winget install JanDeDobbeleer.OhMyPosh -s winget
```

###### (2) Install a font

The way official document recommands didn't apply to me, when I execute the  command as adminstrator:

```powershell
oh-my-posh font install
```

And then select a ramdon one from the list:

<img src="file:///C:/Users/荣耀/AppData/Roaming/marktext/images/2023-12-30-20-13-28-image.png" title="" alt="" width="448">

Use default shortcut 'Ctrl + Shift + ,' to open the 'setting.json' file and add the name of the font I've just downloaded as the document says. After that, the error  immediately occured as:

<img src="file:///C:/Users/荣耀/AppData/Roaming/marktext/images/2023-12-30-20-19-45-image.png" title="" alt="" width="403">

After trying other different fonts I still got the same result, so I try to download([download page]([Nerd Fonts - Iconic font aggregator, glyphs/icons collection, & fonts patcher](https://www.nerdfonts.com/font-downloads))) and manually install a font and finally succeed(remember to add it in the 'setting.json' as before after install your font).

###### (3) Prompt

Use this command to create a profile for your Powershell if you haven't got one

```powershell
New-Item -Path $PROFILE -Type File -Force
```

Use this command to open the profile you just create with notepad.exe

```powershell
notepad $PROFILE
```

Then I add the following line to my profile as the document said(it didn't apply to me)

```
oh-my-posh init pwsh | Invoke-Expression
```

After that, some errors occured(sorry about forgetting them, but adding the OhMyPosh.exe to my Anti-Virus software's whitelist didn't help any). For some good hours I finally found the proper solution on a platform(Thank you, bilibili comments) that I was almost desperate to look for. And the problem is that I got some unicode characters in my OhMyPosh.exe path(if you're curious, they are"鑽ｈ€"). The solution is in the [OhMyPosh FAQ page]([FAQ | Oh My Posh](https://ohmyposh.dev/docs/faq)). Just simply change the line I've put in profile to the following one.

```
$previousOutputEncoding = [Console]::OutputEncoding
[Console]::OutputEncoding = [Text.Encoding]::UTF8
try {
    oh-my-posh init pwsh --config ~/custom.omp.json | Invoke-Expression
} finally {
    [Console]::OutputEncoding = $previousOutputEncoding
}
```

Then dot sourcing the my profile script

```
. $PROFILE
```

###### (4) Costomize

Check all the [themes]([Themes | Oh My Posh](https://ohmyposh.dev/docs/themes)) available, select one and then edit the **PROFILE**, replace the "~/custom.omp.json" with "$env:POSH_THEMES_PATH/theme_name.omp.json". For example, "env:POSH_THEMES_PATH/aliens.omp.json". Fianlly dot sourcing the **PROFILE** like before(If you donot witness the effect, just restart your Powershell).

##### 2. WSL2

###### (1) Homebrew (failed)

I tried to install the Homebrew(check out the official [page]([Homebrew — The Missing Package Manager for macOS (or Linux)](https://brew.sh/))) first but it kept telling me that there's some wrong with my 7890 port(I'm using a proxy to achieve a relatively fast speed). Then I spend a good hours to find out ways to install Homebrew on my WSL2 and finally gave up. By the way, I've got some [methods]([提高ubuntu下访问github的速度_ubuntu git ip加速-CSDN博客](https://blog.csdn.net/hn_tzy/article/details/88903642)) to boast up the downloading speed on github(worked for me though).

###### (2) Download OhMyPosh binary file in github

[Reference]([PowerShell和WSL for Ubuntu安装oh-my-posh_wsl ubuntu 配置oh-my-posd-CSDN博客](https://blog.csdn.net/hys__handsome/article/details/125697108)). Due to the mysterious awfle connection between my WSL2 and github, I went to the github release [page]([Releases · JanDeDobbeleer/oh-my-posh (github.com)](https://github.com/JanDeDobbeleer/oh-my-posh/releases)) and downloaded the [posh-linux-arm64](https://github.com/JanDeDobbeleer/oh-my-posh/releases/download/v19.3.0/posh-linux-arm64) and then moved it to '/usr/local/bin/oh-my-posh' and used `chmod + x` to make it  executable(Assuming you've known some basic Linux commands). 

###### (3) Customize

create a folder to store your theme on your current user's path

```bash
mkdir ~/.poshthemes
```

for the same reason above I have to download the theme.zip file manually(in the same page also and if you couldn't find it, I've got [it](https://github.com/JanDeDobbeleer/oh-my-posh/releases/download/v19.3.0/themes.zip) for you), and then `unzip` it in the folder I just created.

```bash
unzip ~/.poshthemes/theme.zip -d ~/.poshthemes
```

make them all readable and writtable

```bash
chmod u+rw ~/.poshthemes/*.omp.*
```

then you could delete the zip file if you like

```bash
rm ~/.poshthemes/theme.zip
```

To enable OhMyPosh auto-start, add the following line at the end of '/etc/profile'. (remember to replace the 'theme_name' with a real one) 

```
eval "$(oh-my-posh --init --shell bash --config ~/.poshthemes/theme_name.omp.json)"
```

##### Postscripse

All the themes seem too garish for me and just change back to default after all the working. Just for fun. Limited to the author's knowledge, there might be some wrong stuff on blog which you might need to corret them by yourself. 

