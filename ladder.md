# shadowsocks搭建

**shadowsocks 简介**

shadowsocks 是一款自定义协议的代理软件，由于其流量特征不明显，（直到不久前）一直可以稳定提供上网代理。

shadowsocks 客户端会在本地开启一个 socks5 代理，通过此代理的网络访问请求由客户端发送至服务端，服务端发出请求，收到响应数据后再发回客户端。

因此使用 shadowsocks 需要一台墙外的服务器来部署 shadowsocks 服务端。



###1.购买VPS

主流的 VPS（虚拟主机）服务器提供商有三家：

- linode
- digital ocean
- bandwagon

个人自用建议使用[bandwagon](https://bwh1.net/ "bandwagon")。建议购买$19.99/年VPS(加上不记得谁家的优惠码"BWH1ZBPVK"，优惠6%，折后价18.79刀！！！今后续费每年都是这个价！！！)。如需了解更多选购VPS信息，可前往[VPS艹机狂魔的知乎文章](https://zhuanlan.zhihu.com/p/32811900)查看。

接下来就是选购流程啦：

1. **选购VPS**

   登录[bandwagon](https://bwh1.net/ "bandwagon")选购套餐，推荐下面这款，500G/月,如果没有其他用途，只做梯子的话完全够用。

![$indexChoose](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr70k2ASW6BAABpNuZ70Uc165.png)![listChoose](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr71RmAVz6eAADxJWczqIo936.png)

至于为啥要选购KVM,下面是刚刚那位 VPS艹机狂魔 的原文：

```
先定一个基调：OPENVZ架构的，完全不值得购买！

OPENVZ架构内核老旧，同时无法手动切换内核。更别提还有随便超售等等缺点。OPENVZ现在唯一的优势就是支持IPV6了。而且搬瓦工KVM架构的价格已经拉低到和OPENVZ一样的水平，连便宜这样的优势都没有了。现在在去买OPENVZ就是犯傻！务必谨记
```

下面是bangwagon的内容

```
KVM or OpenVZ?
OpenVZ advantages:
	IPv6 support
	Better CPU performance

KVM advantages:
	Full virtualization
	Custom kernel support (BBR, etc)
	Docker support
	Better isolation
	Better network throughput
```

主要影响我们个人使用的点和艹机大大说的也差不多。其实我也不懂，听专业人士的就好了，有兴趣的同学可以去了解下。

2. **确认选购信息**

   接下来就是对下选购清单了，这是年付19.99刀的套餐，主机地址有限六个机房可选，一般选择Los Angles或者New York的主机就可以了，最后添加购物车。

![checkList](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr71z-AMNnnAADisgHVDMI574.png)

3. **验证优惠码**

   进入购物车后，输入优惠码，验证优惠码。

![cart](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr72HaABEzdAACNUSmbcWg621.png)

4. **checkout购物车**

   这样就是18.79刀了！！！然后checkout购物车。

![cartValidated](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr72WuAFDqeAACc8QcBW9s937.png)

5. **提交订单**

   这时候没登录的同学呢就需要登录或者注册填写资料了。支付方式可选择PayPal，或者支付宝（AliPay），同意购买协议之后，完成订单会跳转到订单信息页面。

   PS：国家信息一定要真实，国外有法律规定，如果虚假信息可能涉嫌欺诈，可能会将交易取消。

![pay](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr72iuAJaiDAACxK-bJozs267.png)

6. **确认订单**

   确认一下订单信息就支付吧。之后会跳转到我们熟悉的支付宝支付页面啦。

   ![invoice](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr721KAAtWCAACtiotYgDU823.png)

   

###2.安装shadowsocks

1. **登录管理VPS**

   登录[bandwagon](https://bwh1.net/ "bandwagon")，进入Client Area——>Services——>My Services，在你的服务器清单里选择你的服务器，进入KVM管理页面。

   ![Myservices](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr77b-AcRTlAAERd4wf-is206.png)

   这就是你的KVM控制面板啦！

   ![KVM](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr73rWANarXAAD4BfM2hNU386.png)

2. **安装操作系统**

   进入左边菜单的Install new OS，选择安装centos7（只装过centos，其他的没弄过）。至于为啥要centos7，因为之前踩坑了，那时候用的6，结果后来的自带的python版本不兼容，导致后续shadowsocks安装失败。

3. **安装shadowsocks**

   之前购买vps成功后，提供商会将你的ip、密码发到你的邮箱。然后用[xshell](http://www.netsarang.com/download/down_form.html?code=622)或者其他工具连接你的vps。

   ```
   yum install python-setuptools && easy_install pip
   pip install shadowsocks 
   ```

   这样就安装好了，接下来就是shadowsocks配置了，创建配置文件

   ```
   touch /etc/shadowsocks.json
   vi /etc/shadowsocks.json 
   ```

   输入配置如下

   ```
   { 
      "server":"my_server_ip", 
      "server_port":8888, 
      "local_address": "x.x.x.x",
      "local_port":1080, 
      "password":"****",
      "timeout":300, 
      "method":"rc4-md5", 
      "fast_open": false 
   } 
   ```

   server:服务器IP

   server_port:服务器端口

   local_port:本地端端口

   password:用来加密的密码

   timeout:超时时间（秒）

   method:加密方法，可选择 “bf-cfb”, “aes-256-cfb”, “des-cfb”, “rc4″等 。加密方式官方默认使用aes-256-cfb，推荐使用rc4-md5，因为 RC4比AES速度快好几倍。 

   

   启动shadowsocks

   ``````
   ssserver -c /etc/shadowsocks.json -d start
   ``````

   若无配置文件，在后台可以使用一下命令运行：

   ```
   ssserver -p 8888 -k MyPass -m rc4-md5 -d start
   ```

   停止服务

   ```
   ssserver -c /etc/shadowsocks.json -d stop
   ```

   重启服务

   ```
   ssserver -c /etc/shadowsocks.json -d restart
   ```

   

###3.客户端配置

​	客户端下载地址[百度云盘]([https://pan.baidu.com/s/1GgzKSzEmqctQ5MUvQ4RR1g](https://www.flyzy2005.com/go/go.php?url=https://pan.baidu.com/s/1GgzKSzEmqctQ5MUvQ4RR1g) )，密码：e66v 。

​	Android是4.5版本的apk，Mac是1.7.1版本&1.6.1版本的NG客户端，Windows是2.5版本的可运行文件exe。IOS只能自己安装shadowrocket（app store里的shadowsocks经常下架）。

​	(转自[[flyzy小站](https://www.flyzy2005.com/fan-qiang/shadowsocks/ss-clients-baidu-cloud-download/)])

​	安装之后，服务器——>编辑服务器,内容基本为之前配置文件内容。

​	![shadowsocks](https://dfs.ubtob.com/group1/M00/88/9B/CgpkyFr750OAG8O0AABX4BBJqrs958.png)



###最后，选择“启用系统代理”，就可以大功告成了！！！