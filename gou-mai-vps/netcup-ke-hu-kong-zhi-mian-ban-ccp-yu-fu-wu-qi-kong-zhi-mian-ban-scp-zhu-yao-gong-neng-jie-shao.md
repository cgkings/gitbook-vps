# Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍

**netcup官网：**[https://www.netcup.de/](https://www.netcup.de/?ref=193553)

#### **Netcup客户控制面板CCP (CustomerControlPanel)主要功能介绍** <a href="#0" id="0"></a>

CCP是netcup客户控制面版，主要是包含一些账户信息，和相关主机、域名等服务的控制面版，可以进行终止服务，付款，滥用信息回复，官方支持等。

**1、网址**

CCP地址：[https://www.customercontrolpanel.de/](https://www.customercontrolpanel.de/)

**2、首页概览Overview**

我们登陆上去就是Overview这个目录，右边Outstanding invoices为未付发票提醒。下面Log in to other panels，第一个是自动登陆服务器控制面板SCP，第二个是自动登陆netcup论坛，第一次需要注册后绑定。

&#x20;

**3、产品信息Products**

如图，点开Products可以看到的各项信息。像产品套餐，机房位置，价格，合约时间，下次付款时间等。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/ccp2.png)](https://vpsceping.org/wp-content/uploads/2023/02/ccp2.png)

终止机器Cancellation

如图，第一个是将机器转给别人，生成转移码。

第二个显示时间为，当前时间终止机器后的失效时间，如图就是今天2023年2月11日，如果终止，则在2023年8月22日失效自动取消此机器。（**注意必须提前31天取消，例如买一个月付的RS1000，如果只计划用一个月，则在机器开机的时候就要Cancel product，不然会自动续约到下个月了**）。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/ccp3.png)](https://vpsceping.org/wp-content/uploads/2023/02/ccp3.png)

Storage存储块，250G、500G、1T每月分别是5欧元，8欧元，15欧元，暂时不清楚是固态还是机械盘，一般应该是SSD固态硬盘。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/ccp5.png)](https://vpsceping.org/wp-content/uploads/2023/02/ccp5.png)

**Debian挂载方式：**

安装nfs-common

```
apt-get install nfs-common
```

创建挂载点：

```
mkdir /mnt/storagespace
```

挂载存储空间

需要 CCP 中的存储主机和存储路径。必须相应地替换 STORAGEHOST 和 STORAGEPATH。

```
mount -t nfs STORAGEHOST:/STORAGEPFAD /mnt/storagespace
```

参考资料：[https://www.netcup-wiki.de/wiki/Storagespace\_mounten](https://www.netcup-wiki.de/wiki/Storagespace\_mounten)

**4、付款帐单**

这里会显示所有帐单，未付款状态会显示unpaid，有一个paypal的P图标，点开就可以按流程以paypal付款即可。

注册时的订单由于是付款后才会开机，所以未付帐单提醒15天后会自动消失，不用担心。

后面订单大部分是先开机，后出帐单，因此如果想要取消，必需发邮件联系客服取消沟通，不然可能会产生坏帐，帐号会被禁用。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/ccp6.png)](https://vpsceping.org/wp-content/uploads/2023/02/ccp6.png)

**5、帐号信息**

这里可以直接修改邮箱和帐号密码，查看是否免税，开启两步验证登陆，绑定银行卡帐号。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/CCP7.png)](https://vpsceping.org/wp-content/uploads/2023/02/CCP7.png)

Netcup CCP更多信息可以参考：[https://www.netcup-wiki.de/wiki/Kategorie:CCP](https://www.netcup-wiki.de/wiki/Kategorie:CCP)

***

#### Netcup服务器控制面板SCP（Server Control Panel）主要功能介绍 <a href="#6" id="6"></a>

SCP是netcup服务器的控制面版，控制服务器的基本操作，像开关机，重装系统，查看实时的各项机器数据等。

**1、机器常规信息**

像运行时间，使用的流量（每个自然月为单位计算），可使用的快照数量。下面是机器的基本信息。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/CCP8.png)](https://vpsceping.org/wp-content/uploads/2023/02/CCP8.png)

**2、Control控制**

这里主要是重启机器，红框的就可以了，其它的可能会导致数据遗失。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/SCP2.png)](https://vpsceping.org/wp-content/uploads/2023/02/SCP2.png)

**3、Statistics统计**

主要记录服务器CPU，硬盘IOPS，网络PPS，网络网速等信息。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/SCP3.png)](https://vpsceping.org/wp-content/uploads/2023/02/SCP3.png)

**4、Media媒体选项**

**本地硬盘驱动**

将默认的SCSI改为VIRTIO模式，点击保存即可，可以提升硬盘I/O读写速度。

**Storage optimization存储优化**

像图中显示已用963G，总共1024G，但我服务器实际只用了100多G，所以是对不上的，平时是无所谓的，但快照那里由于剩余空间不足，无法创建快照，因此需要优化，**点Stop Server and optimoze**即可，这时服务器会可能半个小时左右无法访问，后续自动恢复正常。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/SCP4.png)](https://vpsceping.org/wp-content/uploads/2023/02/SCP4.png)

**Images系统镜像**

这里可以重装系统，下面可以选择各系统，一般选择Debian11即可，也可以自己上传系统安装，下面有FTP上传信息。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/SCP5.png)](https://vpsceping.org/wp-content/uploads/2023/02/SCP5.png)

**Snapshots快照**

像我这里由于剩余空间不够，无法创建（如果实际空间足够，可以先存储优化 **Storage optimization**），空间够会显示在线创建还是离线创建，为了数据安全，建议离线创建。

创建的快照，是可以用于同帐号下其它服务器上的，直接在其它服务器上恢复。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/SCP6.png)](https://vpsceping.org/wp-content/uploads/2023/02/SCP6.png)

DVD Drive和Images差不多，也可以自己上传自定义的文件

Rescue System就是安全模式，在此可以启动。

**5、其它杂项**

Network里面可以设置RDNS，主要是配置邮箱时使用。

Access这里需要先关机，然后可以修改root密码。

[![Netcup客户控制面板CCP与服务器控制面板SCP主要功能介绍](https://vpsceping.org/wp-content/uploads/2023/02/ACCESS-1.png)](https://vpsceping.org/wp-content/uploads/2023/02/ACCESS-1.png)

&#x20;
