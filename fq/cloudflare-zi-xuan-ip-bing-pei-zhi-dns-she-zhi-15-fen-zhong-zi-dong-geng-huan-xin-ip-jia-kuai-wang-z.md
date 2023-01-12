# CloudFlare自选IP 并自动修改DNSPOD

## 前言

目前CloufFlare自选IP存在问题，简单总结有以下几点缺陷。

1. 自己A记录填写的CloudFlare IP并不是永久可用，保不齐哪天你填写的CloudFlare IP被GFW给屏蔽了，那你的网站就歇菜了。
2. 自己A记录填写的CloudFlare IP并不是能持续稳定，即使是CloufFlare，服务器也有被攻击的时候，这时你的网站打开速度就会很慢，有时可能会出现打不开的情况。
3. 自己A记录填写的CloudFlare IP并不是对所有人访问速度都快，这一点建站的小伙伴要尤为注意，也许你ping这个Cloudflare IP延迟很低，但是你的站点并不是你一个人访问，你需要的是让国内外每个访问者都能高速访问你的网站。
4. 普通站长很难去找到CloudFLare对不同运营商（移动、联通、电信）访问速度快的IP。

综上，需要本地定期自选IP并修改DNSPOD，本教程可以帮助你定期自动完成这一工作。

## STEP1:下载CloudFlare自选IP脚本

下载地址：[https://github.com/badafans/better-cloudflare-ip/releases](https://github.com/badafans/better-cloudflare-ip/releases)

下载最新版的 [windows.zip](https://github.com/badafans/better-cloudflare-ip/releases/download/20210903/windows.zip)文件【该作者强制更新最新版】

解压到本地

## STEP2:修改脚本

### 1、右键记事本打开"CF优选IP.bat"文件

### 2、根据带宽，修改CloudFlare自选IP带宽需求

比如修改为70Mbps，则将`set /p bandwidth=请设置期望到 CloudFlare 服务器的带宽大小(单位 Mbps):`

修改为`set bandwidth=70`

### 3、获取DNSPOD API Token

&#x20;API Token 生成方法详见：[https://support.dnspod.cn/Kb/showarticle/tsid/227/](https://support.dnspod.cn/Kb/showarticle/tsid/227/)

根据DNSPOD官方教程获取DNSPod Token，并记录【仅出现一次，不记录，之后除了重新生成无法再次查询】

&#x20;一个完整的 Token，组合方式为："ID,Token"（用英文半角逗号分割），比如 ID 为：`13490`,ToKen为：`6b5976c68aba5b14a0558b77c17c3932`。即完整的 Token 为：`13490,6b5976c68aba5b14a0558b77c17c3932` 。

![](<../.gitbook/assets/image (83).png>)



### 4、添加修改自选IP代码

在脚本最后，复制如下代码：

```
echo !anycast!>resolve.txt
echo !anycast!|clip
del data.txt ip.txt CR.txt CRLF.txt cut.txt speed.txt meta.txt
RD /S /Q temp
echo 优选IP已经自动复制到剪贴板
echo 按任意键关闭
pause>nul
```

粘贴替换为如下代码:

```
echo !anycast!>resolve.txt
del data.txt ip.txt CR.txt CRLF.txt cut.txt speed.txt meta.txt
RD /S /Q temp
echo 优选IP已经自动保存在resolve.txt文件，即将自动修改DNSPOD记录
curl -X POST https://dnsapi.cn/Record.Modify -d "login_token="完整token"&format=json&domain=你的二级域名&record_id=通过记录列表命令查询获取&sub_domain=你的子域名&value=!anycast!&record_type=A&record_line_id=查询获得"
echo 优选IP !anycast! 修改完毕，自动退出！
exit
```

{% hint style="info" %}
注意：curl的那行代码为通过API修改DNS记录关键代码

其中的`record_id`_和`record_line_id需要通过如下命令获得`_

```
curl -X POST https://dnsapi.cn/Record.List -d "login_token="完整token"&format=json&format=json&domain=你的二级域名&sub_domain=你的子域名&record_type=A"
```
{% endhint %}

### 5、为脚本添加计划任务

windows里的计划任务设置还是比较容易的，不会自行百度，计划任务间隔自行决定吧

## 总结

教程已经很详细了，不清楚的自行百度或谷歌。
