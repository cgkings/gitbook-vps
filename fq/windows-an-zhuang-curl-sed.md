# windows安装curl sed

### 1.安装Chocolatey包安装管理器

管理员运行 powershell.exe，运行如下代码：

```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

等待几秒钟，让命令完成，如果您没有看到任何错误，您就可以使用 Chocolatey 了！

键入`choco`或 `choco -?`查看[入门](https://docs.chocolatey.org/en-us/getting-started)以获取使用说明

### 2.安装curl、jq

```
chocolatey install curl
```

```
chocolatey install jq
```

