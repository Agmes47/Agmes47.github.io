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

###### (3)Prompt

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

