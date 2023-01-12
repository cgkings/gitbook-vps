# 申请泛域名证书

## 前言

一、安装x-ui，自行进入项目地址安装\
[https://github.com/sprov065/x-ui](https://github.com/sprov065/x-ui)

二、有cloudflare.com账号，申请域名并解析到了cloudflare

三、证书申请【debian和Ubuntu系统】\[文中所有涉及域名my.com改为你自己的域名]

dns申请，好处是可以申请泛域名证书（即一张证书可以管整个域名和他的子域名）【推荐方式】

## 一、安装acme.sh脚本

以下所有步骤参照了[acme.sh官方wiki](https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E)，由于脚本更新频繁，将来以下步骤可能不适合，请大家直接参考 wiki。

### 1、安装前准备

```
apt update -y && apt install -y curl && apt install -y socat
```

### 2、安装 acme.sh脚本

```
curl  https://get.acme.sh | sh -s email=my@example.com
```

{% hint style="info" %}
安装脚本进行了以下几步:

1、把 acme.sh 安装到你的 **home** 目录下，并创建 一个 bash 的 alias, 方便你的使用: `alias acme.sh=~/.acme.sh/acme.sh`

2、自动为你创建 cronjob, 每天 0:00 点自动检测所有的证书, 如果快过期了, 需要更新, 则会自动更新证书.
{% endhint %}

### 3、自动升级acme.sh

{% tabs %}
{% tab title="开启自动升级" %}
```
acme.sh  --upgrade  --auto-upgrade
```
{% endtab %}

{% tab title="关闭自动升级" %}
```
acme.sh --upgrade  --auto-upgrade  0
```
{% endtab %}
{% endtabs %}

## 二、申请泛域名证书

dns申请，好处是可以申请泛域名证书（即一张证书可以管整个域名和他的子域名）【推荐方式】

**acme.sh** 目前支持 cloudflare, dnspod, cloudxns, godaddy 以及 ovh 等数十种解析商的自动集成.

{% tabs %}
{% tab title="cloudflare" %}
**获取dns解析商API**

Cloudflare Domain API 提供了两种自动颁发证书的方法。

1、全局API密钥；2、API token

个人认为，全局API密钥比较简单一点

获取 Cloudflare 的 API 信息\
[https://dash.cloudflare.com/profile](https://dash.cloudflare.com/profile)\
找到 Global API Key，记录信息后修改export CF\_Key和export CF\_Email后面的值并执行这两条命令。

```
export CF_Key="xxxxxxx"
export CF_Email="ddddd5@ss.com"
```

其中CF\_Key是你的Global API Key，CF\_Email是你注册cloudflare的邮箱。

该`CF_Key`和`CF_Email`或`CF_Token`和`CF_Account_ID`将被保存`~/.acme.sh/account.conf`，需要时会被重用。

**开始申请证书**

```
acme.sh --issue --dns dns_cf -d my.com -d '*.my.com'
```

**安装dns方式申请的证书**

注意, 默认生成的证书都放在安装目录下: `~/.acme.sh/`, 请不要直接使用此目录下的文件, 例如: 不要直接让 nginx/apache 的配置文件使用这下面的文件. 这里面的文件都是内部使用, 而且目录结构可能会变化.

正确的使用方法是使用 `--install-cert` 命令

```
acme.sh --install-cert -d my.com -d '*.my.com' --key-file /root/.ssh/private.key --fullchain-file /root/.ssh/cert.crt --reloadcmd "x-ui restart"
```

上面的 /root/.ssh/private.key，以及 /root/cert.crt，是把密钥和证书安装到/root/.ssh/.ssh/目录，并改名为private.key和cert.crt

证书申请完毕后，打开x-ui后台-面板设置&#x20;

“面板证书公钥文件路径”里填写/root/.ssh/cert.crt&#x20;

“面板证书密钥文件路径”里填写/root/.ssh/private.key
{% endtab %}

{% tab title="dnspod" %}
**获取dns解析商API**

以 dnspod 为例, 你需要先登录到 dnspod 账号, 生成你的 api id 和 api key, 都是免费的. 然后:

```
export DP_Id="1234"
export DP_Key="sADDsdasdgdsf"
```

**开始申请证书**

```
acme.sh --issue --dns dns_dp -d aa.com -d '*.aa.com'
```

证书就会自动生成了. 这里给出的 api id 和 api key 会被自动记录下来, 将来你在使用 dnspod api 的时候, 就不需要再次指定了. 直接生成就好了:

```
acme.sh --issue --dns dns_dp -d mydomain2.com
```

**安装dns方式申请的证书**

注意, 默认生成的证书都放在安装目录下: `~/.acme.sh/`, 请不要直接使用此目录下的文件, 例如: 不要直接让 nginx/apache 的配置文件使用这里面的文件. 这里面的文件都是内部使用, 而且目录结构可能会变化.

正确的使用方法是使用 `--install-cert` 命令

```
acme.sh --install-cert -d my.com -d '*.my.com' --key-file /root/.ssh/private.key --fullchain-file /root/.ssh/cert.crt --reloadcmd "x-ui restart"
```

上面的/root/.ssh/private.key，以及/root/cert.crt，是把密码和证书安装到/root/.ssh/ .ssh/目录，并改名为private.key和cert.crt

证书申请完毕后，打开x-ui后台-面板设置

“面板证书公钥文件路径”里填写/root/.ssh/cert.crt&#x20;

“面板证书密钥文件路径”里填写/root/.ssh/private.key
{% endtab %}

{% tab title="ali" %}
添加 dns api 相关 key 和秘钥。

```
export Ali_Key="ali-key"
export Ali_Secret="ali-secret"
```

会将秘钥信息保存在： `~/.acme.sh/account.conf` 中，方便下面申请证书和以后证书续期使用。

申请证书：

```
acme.sh --issue --dns dns_ali -d panghuli.cn -d *.panghuli.cn
```

该命令会将申请的证书放在 `~/.acme.sh/panghuli.cn/` 下。

该命令会添加定时任务，自动检测证书是否过期，并且续期。

```
crontab -l
50 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
```

列出已经申请的证书信息：

```
./acme.sh --list
```
{% endtab %}
{% endtabs %}

更详细的 api 用法: https://github.com/Neilpang/acme.sh/blob/master/dnsapi/README.md

