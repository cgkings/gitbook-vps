# 无需VPS用CLOUDFLARE的Workers 搭建vless 免费白嫖全世界各地节点

想要搭建免得vless节点，全世界任何地方的节点，现实中去不到，今天你在这能学到，有了这些节点，你可以学到很多东西，至于怎么利用那要看你自己想法，开始吧如何将你的ip挂在全球各地任何地方。

## &#x20;注册cloudflare账号

首先你的有一个cloudflare 的账号还有一个解析好的域名托管在cloudflare ，如果你没有绑定域名的话或许你还没注册的话，[《请点击查看注册教程》](https://heibai.pro/zero-tust/)

## 创建workers脚本

开始第一步打开cloudflare账号卡纳到左边点开Workers

&#x20;

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-215737-137x300.png)

创建wokers ，

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-220358-300x203.png) ![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-220403.png)

名称随便你填写，如何右下角点击部署。

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-220721-300x199.png)、

然后点击编辑代码

再把原有的代码全部给清掉，

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-221100-300x146.png)

记住清掉原有的全部代码，然后在GitHub拿一个作者的编辑好的代码，复制粘贴过去，下面是代码地址

[点击浏览复制代码](https://github.com/zizifn/edgetunnel/blob/main/src/worker-vless.js)

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-222056-300x176.png)

点击右上角复制粘贴到刚刚cloudflare 后台编辑代码哪里

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-222422-300x185.png)

我们要编辑第五行的uuid跟第九行的代理ip

这个代理IP，指的是一个区域的服务商的ip，后面再教大家怎么换ip选全国各地区域的ip地址。现在先暂时用这个我今天刚刚生成的美国ip   47.243.86.79

UUID用v2rayN生成一条就行了   没有v2ray的下载一个[https://github.com/2dust/v2rayN/releases/tag/6.23](https://github.com/2dust/v2rayN/releases/tag/6.23)

或许你可以Google在线搜索UUid生成，生成一条也行[https://1024tools.com/uuid](https://1024tools.com/uuid)

下载好了之后，以管理员打开，点开左上角服务器添加VLESS服务器，然后点UUID那一条生成，这样我们就有一条UUID了，记得保存，后面还要用到（当然你也可以去搜索uuid在线生成也行）

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-223819-256x300.png) ![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-223838-300x182.png)

再把我提供的IP地址，跟教你生成的UUID写在刚刚cloudflare后台第五行，跟第九行下面如图（要写在双引号之间）

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-224407-300x232.png) ![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-224533-300x223.png)

写好了之后，然后右上角点击保存并部署。这里部署就![🆗](https://s.w.org/images/core/emoji/14.0.0/svg/1f197.svg)了，我们下一步创建节点。

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-224718-300x237.png) ![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-224810-300x214.png)

回到刚刚的起点，我们点开workers ，再点开你刚刚创建的名称，进去下一步，点指标右边的触发器。(提示；必须你已经绑定好了域名、在cloudflare 下面这一步才有效果，如果你还没解析域名（[点击查看解析教程](https://heibai.pro/cloud-flare%E5%A6%82%E4%BD%95%E8%A7%A3%E6%9E%90%E5%9F%9F%E5%90%8D%E6%89%98%E7%AE%A1/))

上面如图，然后点添加自定义域，前缀随便你写吧后面就是你解析的域名，我这里美国IP我就写ub.hbbaby.top

输入好了之后点击左边的添加自定义域名，添加了之后等待1分钟证书就会显示有效。

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-225304-300x127.png) ![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-225815-300x84.png)

有效之后我们点开，域名哪里，也就是你刚刚填写的域名点一下跳转到如下的页面

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-230224-300x84.png)

地址上面添加/bbcb043b-0417-4a1f-b80c-d095b30b788b回车

我们的vless节点就出来啦！

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-01-230417-300x144.png)

复制这条节点vless的节点，一整条复制到你的v2ratn就大功告成了。

&#x20;

注意；如果测速延迟-1的话，在你网站哪里概述，点开你的网站把你的under Attack的模式给关了就ok了

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-000854-300x128.png)

## 获取最快的cloudflare世界IP

代理ip教程我继续教大家怎么制作各国你想要的区域节点，比如你要美国洛杉矶，日本东京，韩国首尔==代理ip都可以做到

如何自己做自己想的ip地址，比如你想要日本东京，或者美国洛杉矶，等等，指定国家指定区域的代理ip那么你就找对 文了。

如果你还不知道怎么搭建自己的的节点请看《[搭建节点](https://heibai.pro/dajianjiedian/)》关键免费

### 第一步

到飞机这里[https://t.me/cf\_push ](https://t.me/cf\_push)

这里有很多cf中转的代理ip 有机器人每天维护

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-042354-271x300.png)

把这里的ip全部下载下来建一个文档。

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-042948-300x105.png)

现在吧这些ip给合并起来，输入CMD回车，type \*.txt>>文件名后缀txt

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-044345-300x240.png)

然后我们就得到一个合并起来的iptxt文档，把他们都给删了，留着碍眼。

### 批量获取IP所在地

打开这个网站[https://reallyfreegeoip.org/bulk](https://reallyfreegeoip.org/bulk)

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-044811-300x238.png)

这个网站是用来ip分类，分那个国家，那个城市第一次安装会需要你安装一个扩展，按照提示操作就行

把你刚刚那些ip合并一起的，复制粘贴到这个网站好了之后点击Bulk Lookup

等待分类，

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-045636-300x82.png)

几万条，等待还是有需要时间的，如果你等不急，你可以点下载就会得到一个csv文件

右键筛选 ，

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-050025-300x210.png)

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-050159-247x300.png) ![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-050529-295x300.png)

选择你要的国家地区，B选国家，H选国家的地区。然后保存起来。

### 批量测速

到GitHub 下载一个IP测速exe[https://github.com/XIU2/CloudflareSpeedTest/releases](https://github.com/XIU2/CloudflareSpeedTest/releases)

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-051137-300x157.png)

选择你对应的版本下载，下载好了之后解压在你刚刚筛选好的IP同一个文件里。

之后打开CloudflareST.exe

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-053328-300x124.png)

![](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-053434-300x157.png)

打开之后等它选延迟低的ip稍等就![🆗](https://s.w.org/images/core/emoji/14.0.0/svg/1f197.svg)了，你就会获得十条速度比较好的ip

到这里你就已经学会怎么选各国各地区的IP了

