# Caddy2 简明教程

Caddy 是一个 Go 编写的 Web 服务器，类似于 Nginx，Caddy 提供了更加强大的功能，随着 v2 版本发布 Caddy 已经可以作为中小型站点 Web 服务器的另一个选择；相较于 Nginx 来说使用 Caddy 的优势如下:

* 自动的 HTTPS 证书申请(ACME HTTP/DNS 挑战)
* 自动证书续期以及 OCSP stapling 等
* 更高的安全性包括但不限于 TLS 配置以及内存安全等
* 友好且强大的配置文件支持
* 支持 API 动态调整配置(有木有人可以搞个 Dashboard？)
* 支持 HTTP3(QUIC)
* 支持动态后端，例如连接 Consul、作为 k8s ingress 等
* 后端多种负载策略以及健康检测等
* 本身 Go 编写，高度模块化的系统方便扩展(CoreDNS 基于 Caddy1 开发)
* ……

就目前来说，Caddy 对于我个人印象唯一的缺点就是性能没有 Nginx 高，但是这是个仁者见仁智者见智的问题；相较于提供的这些便利性，在性能可接受的情况下完全有理由切换到 Caddy。

### 一、编译 Caddy2

> 注意: 在 Caddy1 时代，Caddy 官方发布的预编译二进制文件是不允许进行商业使用的，Caddy2 以后已经全部切换到 Apache 2.0 License，具体请参考 [issue#2786](https://github.com/caddyserver/caddy/issues/2786)。

在默认情况下 Caddy2 官方提供了预编译的二进制文件，以及[自定义 build 下载页面](https://caddyserver.com/download)，不过对于需要集成一些第三方插件时，我们仍需采用官方提供的 [xcaddy](https://github.com/caddyserver/xcaddy) 来进行自行编译；以下为具体的编译过程:

#### 1.1、Golang 环境安装

> **本部分编译环境默认为 Ubuntu 20.04 系统，同时使用 root 用户，其他环境请自行调整相关目录以及配置；编译时自行处理好科学上网相关配置，也可以直接用国外 VPS 服务器编译。**

首先下载 go 语言的 SDK 压缩包，其他平台可以从 [https://golang.org/dl/](https://golang.org/dl/) 下载对应的压缩包:

```
wget https://golang.org/dl/go1.15.6.linux-amd64.tar.gzCopy
```

下载完成后解压并配置相关变量:

```
# 解压
tar -zxvf go1.15.6.linux-amd64.tar.gz

# 移动到任意目录
mkdir -p /opt/devtools
mv go /opt/devtools/go

# 创建 go 相关目录
mkdir -p ${HOME}/gopath/{src,bin,pkg}

# 调整变量配置，将以下变量加入到 shell 初始化配置中
# bash 用户请编辑 ~/.bashrc
# zsh 用户请编辑 ~/.zshrc
export GOROOT='/opt/devtools/go'
export GOPATH="${HOME}/gopath"
export GOPROXY='https://goproxy.cn' # 如果已经解决了科学上网问题，GOPROXY 变量可以删除，否则可能会起反作用
export PATH="${GOROOT}/bin:${GOPATH}/bin:${PATH}"

# 让配置生效
# bash 用户替换成 ~/.basrc
# 重新退出登录也可以
source ~/.zshrcCopy
```

配置完成后，应该在命令行执行 `go version` 有成功返回:

```
bleem ➜ ~ go version
go version go1.15.6 linux/amd64Copy
```

#### 1.2、安装 xcaddy

按照官方文档直接命令行执行 `go get -u github.com/caddyserver/xcaddy/cmd/xcaddy` 安装即可:

```
bleem ➜ ~ go get -u github.com/caddyserver/xcaddy/cmd/xcaddy
go: downloading github.com/caddyserver/xcaddy v0.1.7
go: found github.com/caddyserver/xcaddy/cmd/xcaddy in github.com/caddyserver/xcaddy v0.1.7
go: downloading github.com/Masterminds/semver/v3 v3.1.0
go: github.com/Masterminds/semver/v3 upgrade => v3.1.1
go: downloading github.com/Masterminds/semver/v3 v3.1.1
.....Copy
```

安装完成后应当在命令行可以直接执行 `xcaddy` 命令:

```
# xcaddy 并没有提供完善的命令行支持，所以 `--help` 报错很正常
bleem ➜  ~ xcaddy --help
go: cannot match "all": working directory is not part of a module
2021/01/07 12:15:56 [ERROR] exec [go list -m -f={{if .Replace}}{{.Path}} => {{.Replace}}{{end}} all]: exit status 1:Copy
```

#### 1.3、编译 Caddy2

编译之前系统需要安装 `jq`、`curl`、`git` 命令，没有的请使用 `apt install -y curl git jq` 命令安装；

自行编译的目的是增加第三方插件方便使用，其中官方列出的插件可以从 [Download](https://caddyserver.com/download) 页面获取到:

[![XkJZ2R1609993722272](https://cdn.oss.link/markdown/XkJZ2R1609993722272.png)](https://cdn.oss.link/markdown/XkJZ2R1609993722272.png)

[XkJZ2R1609993722272](https://cdn.oss.link/markdown/XkJZ2R1609993722272.png)

其他插件可以从 GitHub 上寻找或者自行编写，整理好这些插件列表以后只需要使用 `xcaddy` 编译即可:

```
# 获取最新版本号，其实直接去 GitHub realse 页复制以下就行
# 这里转化为脚本是为了方便自动化
export version=$(curl -s "https://api.github.com/repos/caddyserver/caddy/releases/latest" | jq -r .tag_name)

# 使用 xcaddy 编译
xcaddy build ${version} --output ./caddy_${version} \
        --with github.com/abiosoft/caddy-exec \
        --with github.com/caddy-dns/cloudflare \
        --with github.com/caddy-dns/dnspod \
        --with github.com/caddy-dns/duckdns \
        --with github.com/caddy-dns/gandi \
        --with github.com/caddy-dns/route53 \
        --with github.com/greenpau/caddy-auth-jwt \
        --with github.com/greenpau/caddy-auth-portal \
        --with github.com/greenpau/caddy-trace \
        --with github.com/hairyhenderson/caddy-teapot-module \
        --with github.com/kirsch33/realip \
        --with github.com/porech/caddy-maxmind-geolocation \
        --with github.com/caddyserver/format-encoder \
        --with github.com/mholt/caddy-webdavCopy
```

编译过程日志如下所示，稍等片刻后将会生成编译好的二进制文件:

[![Kr2tG61609993987722](https://cdn.oss.link/markdown/Kr2tG61609993987722.png)](https://cdn.oss.link/markdown/Kr2tG61609993987722.png)

[Kr2tG61609993987722](https://cdn.oss.link/markdown/Kr2tG61609993987722.png)

编译成功后可以通过 `list-modules` 子命令查看被添加的插件是否成功编译到了 caddy 中:

```
bleem ➜  ~ ./caddy_v2.3.0 list-modules
admin.api.load
admin.api.metrics
caddy.adapters.caddyfile
caddy.listeners.tls
caddy.logging.encoders.console
caddy.logging.encoders.filter
caddy.logging.encoders.filter.delete
caddy.logging.encoders.filter.ip_mask
caddy.logging.encoders.formatted
caddy.logging.encoders.json
caddy.logging.encoders.logfmt
caddy.logging.encoders.single_field
caddy.logging.writers.discard
caddy.logging.writers.file
caddy.logging.writers.net
caddy.logging.writers.stderr
caddy.logging.writers.stdout
caddy.storage.file_system
dns.providers.cloudflare
dns.providers.dnspod
dns.providers.duckdns
dns.providers.gandi
dns.providers.route53
exec
http
http.authentication.hashes.bcrypt
http.authentication.hashes.scrypt
http.authentication.providers.http_basic
http.authentication.providers.jwt
......Copy
```

### 二、安装 Caddy2

#### 2.1、宿主机安装

宿主机安装 Caddy2 需要使用 systemd 进行守护，幸运的是 Caddy2 官方提供了各种平台的安装包以及 [systemd 配置文件仓库](https://github.com/caddyserver/dist)；目前推荐的方式是直接采用包管理器安装标准版本的 Caddy2，然后替换自编译的可执行文件:

```
# 安装标准版本 Caddy2
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/cfg/gpg/gpg.155B6D79CA56EA34.key' | sudo apt-key add -
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/cfg/setup/config.deb.txt?distro=debian&version=any-version' | sudo tee -a /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy

# 替换二进制文件
systemctl stop caddy
rm -f /usr/bin/caddy
mv ./caddy_v2.3.0 /usr/bin/caddyCopy
```

#### 2.2、Docker 安装

Docker 用户可以通过 Dockerfile 自行编译 image，目前我编写了一个基于 xcaddy 的 [Dockerfile](https://github.com/mritd/dockerfile/blob/master/caddy/Dockerfile)，如果有其他插件需要集成自行修改重新编译即可；当前 Dockerfile 预编译的镜像已经推送到了 [Docker Hub](https://hub.docker.com/repository/docker/mritd/caddy) 中，镜像名称为 `mritd/caddy`。

### 三、配置 Caddy2

Caddy2 的配置文件核心采用 json，但是 json 可读性不强，所以官方维护了一个转换器，抽象出称之为 Caddyfile 的新配置格式；关于 Caddyfile 的完整语法请查看官方文档 [https://caddyserver.com/docs/caddyfile](https://caddyserver.com/docs/caddyfile)，本文仅做一些基本使用的样例。

#### 3.1、配置片段

Caddyfile 支持类似代码中 function 一样的配置片段，**这些配置片段可以在任意位置被 `import`，同时可以接受参数，**以下为配置片断示例:

```
# 括号内为片段名称，可以自行定义
(TLS) {
    protocols tls1.2 tls1.3
    ciphers TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256
}

# 在任意位置可以引用此片段从而达到配置复用
import TLSCopy
```

#### 3.2、配置模块化

`import` 指令除了支持引用配置片段以外，还支持引用外部文件，同时支持通配符，有了这个命令以后我们就可以方便的将配置文件进行模块化处理:

```
# 引用外部的 /etc/caddy/*.caddy
import /etc/caddy/*.caddyCopy
```

#### 3.3、站点配置

针对于站点域名配置，Caddyfile 比较自由化，其格式如下:

```
地址 {
    站点配置
}Copy
```

关于这个 “地址” 接受多种格式，以下都为合法的地址格式:

* `localhost`
* `example.com`
* `:443`
* `http://example.com`
* `localhost:8080`
* `127.0.0.1`
* `[::1]:2015`
* `example.com/foo/*`
* `*.example.com`
* `http://`

#### 3.4、环境变量

Caddyfile 支持直接引用系统环境变量，通过此功能可以将一些敏感信息从配置文件中剔除:

```
# 引用环境变量 GANDI_API_TOKEN
dns gandi {$GANDI_API_TOKEN}Copy
```

#### 3.5、配置片段参数支持

针对于配置片段，Caddyfile 还支持类似于函数代码的参数支持，通过参数支持可以让外部引用时动态修改配置信息:

```
(LOG) {
    log {
        format json  {
            time_format "iso8601"
        }
        # "{args.0}" 引用传入的第一个参数，此处用于动态传入日志文件名称
        output file "{args.0}" {
            roll_size 100mb
            roll_keep 3
            roll_keep_for 7d
        }
    }
}

# 引用片段
import LOG "/data/logs/mritd.com.log"Copy
```

#### 3.6、自动证书申请

在启动 Caddy2 之前，如果目标域名(例如: `www.example.com`)已经解析到了本机，那么 Caddy2 启动后会尝试自动通过 ACME HTTP 挑战申请证书；如果期望使用 DNS 的方式申请证书则需要其他 DNS 插件支持，比如上面编译的 `--with github.com/caddy-dns/gandi` 为 gandi 服务商的 DNS 插件；关于使用 DNS 挑战的配置编写方式需要具体去看其插件文档，目前 gandi 的配置如下:

```
tls {
	dns gandi {env.GANDI_API_TOKEN}
}Copy
```

配置完成后 Caddy2 会通过 ACME DNS 挑战申请证书，**值得注意的是即使通过 DNS 申请证书默认也不会申请泛域名证书，如果想要调整这种细节配置请使用 json 配置或管理 API。**

#### 3.7、完整模块化配置样例

了解了以上基础配置信息，我们就可以实际编写一个站点配置了；以下为本站的 Caddy 配置样例:

目录结构:

```
caddy
├── Caddyfile
├── mritd.com.caddy
└── mritd.me.caddyCopy
```

**3.7.1、Caddyfile**

**Caddyfile 主要包含一些通用的配置，并将其抽到配置片段中，类似与 nginx 的 `nginx.conf` 主配置；在最后部分通过 `import` 关键字引入其他具体站点配置，类似 nginx 的 `vhost` 配置。**

```
(LOG) {
    log {
        # 日志格式参考 https://github.com/caddyserver/format-encoder 插件文档
        format formatted "[{ts}] {request>remote_addr} {request>proto} {request>method} <- {status} -> {request>host} {request>uri} {request>headers>User-Agent>[0]}"  {
            time_format "iso8601"
        }
        output file "{args.0}" {
            roll_size 100mb
            roll_keep 3
            roll_keep_for 7d
        }
    }
}

(TLS) {
    # TLS 配置采用 https://mozilla.github.io/server-side-tls/ssl-config-generator/ 生成，SSL Labs 评分 A+
    protocols tls1.2 tls1.3
    ciphers TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256
}

(HSTS) {
    # HSTS (63072000 seconds)
    header / Strict-Transport-Security "max-age=63072000"
}

(ACME_GANDI) {
    # 从环境变量获取 GANDI_API_TOKEN
    dns gandi {$GANDI_API_TOKEN}
}

# 聚合上面的配置片段为新的片段
(COMMON_CONFIG) {
    # 压缩支持
    encode zstd gzip

    # TLS 配置
    tls {
        import TLS
        import ACME_GANDI
    }

    # HSTS
    import HSTS
}

# 开启 HTTP3 实验性支持
{
    servers :443 {
        protocol {
            experimental_http3
        }
    }
}

# 引入其他具体的站点配置
import /etc/caddy/*.caddyCopy
```

**3.7.2、mritd.com.caddy**

**`mritd.com.caddy` 为主站点配置，主站点配置内主要编写一些路由规则，TLS 等都从配置片段引入，这样可以保持统一。**

```
www.mritd.com {
    # 重定向到 mritd.com(默认 302)
    redir https://mritd.com{uri}

    # 日志
    import LOG "/data/logs/mritd.com.log"

    # TLS、HSTS、ACME 等通用配置
    import COMMON_CONFIG
}

mritd.com {
    # 路由
    route /* {
        reverse_proxy mritd_com:80
    }

    # 日志
    import LOG "/data/logs/mritd.com.log"

    # TLS、HSTS、ACME 等通用配置
    import COMMON_CONFIG
}Copy
```

**3.7.3、mritd.me.caddy**

**`mritd.me.caddy` 为老站点配置，目前主要将其 301 到新站点即可。**

```
www.mritd.me {
    # 重定向到 mritd.com
    # 最后的 "code" 支持三种参数
    # temporary => 302
    # permanent => 301
    # html => HTML document redirect
    redir https://mritd.com{uri} permanent

    # 日志
    import LOG "/data/logs/mritd.com.log"

    # TLS、HSTS、ACME 等通用配置
    import COMMON_CONFIG
}

mritd.me {
    # 重定向
    redir https://mritd.com{uri} permanent

    # 日志
    import LOG "/data/logs/mritd.com.log"

    # TLS、HSTS、ACME 等通用配置
    import COMMON_CONFIG
}Copy
```

### 四、启动与重载

配置文件编写完成后，通过 `systemctl start caddy` 可启动 caddy 服务器；每次配置修改后可以通过 `systemctl reload caddy` 进行配置重载，重载期间 caddy 不会重启(实际上调用 `caddy reload` 命令)，**当配置文件书写错误时，重载只会失败，不会影响正在运行的 caddy 服务器。**

### 五、总结

本文只是列举了一些简单的 Caddy 使用样例，在强大的插件配合下，Caddy 可以实现各种 “神奇” 的功能，这些功能依赖于复杂的 Caddy 配置，Caddy 配置需要仔细阅读[官方文档](https://caddyserver.com/docs/caddyfile/directives)，关于 Caddyfile 的每个配置段在文档中都有详细的描述。

值得一提的是 Caddy 本身内置了丰富的插件，例如内置 “file\_server”、内置各种负载均衡策略等，这些插件组合在一起可以实现一些复杂的功能；Caddy 是采用 go 编写的，官方也给出了详细的[开发文档](https://caddyserver.com/docs/extending-caddy)，相较于 Nginx 来说通过 Lua 或者 C 来开发编写插件来说，Caddy 的插件开发上手要容易得多；Caddy 本身针对数据存储、动态后端、配置文件转换等都内置了扩展接口，这为有特定需求的扩展开发打下了良好基础。

最终总结，综合来看目前 Caddy2 的性能损失可接受的情况下，相较于 Nginx 绝对是个绝佳选择，各种新功能都能够满足现代化 Web 站点的需求，真香警告。
