# 获取最快的cloudflare世界IP

如何自己做自己想的ip地址，比如你想要日本东京，或者美国洛杉矶，等等，指定国家指定区域的代理ip那么你就找对 文了。

如果你还不知道怎么搭建自己的的节点请看《[搭建节点](https://heibai.pro/dajianjiedian/)》关键免费

## 1.获取初选CF世界IP

到飞机这里[https://t.me/cf\_push](https://t.me/cf\_push)

这里有很多cf中转的代理ip 有机器人每天维护

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-042354-271x300.png)

把这里的ip全部下载下来建一个文档。

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-042948-300x105.png)

## 2.合并处理IP文档

现在把这些ip给合并起来生成一个以当前日期作为文件名的txt文件，如20230911.txt，输入CMD回车

```
type *.txt>>%date:~0,4%%date:~5,2%%date:~8,2%.txt
```

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-044345-300x240.png)

然后我们就得到一个合并起来的iptxt文档，把他们都给删了，留着碍眼。

## 3.批量获取IP归属地

然后打开这个网站https://reallyfreegeoip.org/bulk

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-044811-300x238.png)

这个网站是用来ip分类，分那个国家，那个城市第一次安装会需要你安装一个扩展，按照提示操作就行

把你刚刚那些ip合并一起的，复制粘贴到这个网站好了之后点击Bulk Lookup

等待分类，

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-045636-300x82.png)

几万条，等待还是有需要时间的，如果你等不急，你可以点下载就会得到一个csv文件

右键筛选 ，

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-050025-300x210.png)

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-050159-247x300.png) ![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-050529-295x300.png)

选择你要的国家地区，B选国家，H选国家的地区。然后保存起来。

## 4.批量IP测速

选好了之后到GitHub 下载一个IP测速exe

https://github.com/XIU2/CloudflareSpeedTest/releases

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-051137-300x157.png)

选择你对应的版本下载，下载好了之后解压在你刚刚筛选好的IP同一个文件里。

之后打开CloudflareST.exe

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-053328-300x124.png)

![img](https://heibai.pro/wp-content/uploads/2023/09/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE-2023-09-02-053434-300x157.png)

打开之后等它选延迟低的ip稍等就OK了，你就会获得十条速度比较好的ip

到这里你就已经学会怎么选各国各地区的IP了
