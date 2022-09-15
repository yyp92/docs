# DNS

## 为什么需要DNS

在上网冲浪时，我们在浏览器输入的都是网站的域名，而不是 Web 服务器的 IP 地址。域名和 IP 地址的关系就像我们手机通讯录那样，用对方的名字来对应它的手机号码。这样每次拨打电话的时候，直接在通讯录里查名字就可以找到对方的手机号码，而不需要去记忆对方的手机号码。

IP 地址和手机号码都是一长串的数字，如果要去记忆它们，还是挺费劲的。

所以，为了网民上网冲浪舒服点，就搞了个 DNS 服务器，也就是域名解析服务器，它可以通过我们输入的域名来找到 Web 服务器的 IP 地址，然后浏览器再向 Web 服务器发送请求。

问题来了，既然域名方便记忆，那为什么不直接使用域名进行通信呢？因为IP地址是固定长度的，IPv4是32位，IPv6是128位，而域名是变长的，不便于计算机处理。

所以一句话总结就是：IP地址是面向主机的，域名是面向用户的。

## DNS域名的结构

域名都是用句点来分隔的，比如 `www.server.com`，这里的句点代表了不同层次之间的界限，越靠右的位置表示其层级越高。

根域是在最顶层，它的下一层就是 com 顶级域，再下面是 server.com，所以域名的层级关系类似一个树状结构：

![](../../\imgs\dns-1.jpg)

上面的树状结构的域名，也有对应的一些服务器：

- 根DNS服务器

- 顶级域DNS服务器（com）

- 授权DNS服务器（server.com），这个服务器是购买域名时，对应厂家的服务器，比如阿里云上买的域名，授权DNS服务器就是阿里云的某个DNS服务器。

- 本地DNS，当一个主机发出DNS查询请求时，这个查询请求首先发给本地域名服务器（本地主机hosts、/etc/resolv.conf和本地网卡配置中指定的DNS服务器）

以oss.console.aliyun.com为例：

![](../../\imgs\dns-2.png)

顶级域名：顶级域名分为国家顶级域名、通用顶级域名、反向域名

- 国家顶级域名：中国cn，美国us，英国uk

- 通用顶级域名：com公司企业、edu教育机构、gov政府部门、int国际组织、net网络、org非营利组织

- 反向域名：arpa，用于PTR查询（IP地址转换为域名）。Linux的sshd配置文件把UseDNS这一样设置为no就是禁用这个PTR查询来提升连接速度的。

## 域名解析过程

我们在浏览器输入一个域名，例如www.baidu.com，回车以后：

1. 客户端首先会发出一个 DNS 请求，问 www.baidu.com的 IP 是什么，这个请求会发给本地 DNS 服务器（也就是客户端的hosts、/etc/resolv.conf和网络配置中填写的 DNS 服务器地址，依次查询）。

2. 本地域名服务器收到客户端的请求后，如果缓存里的表格能找到 www.baidu.com，则它直接返回 IP 地址。如果没有，本地 DNS 会去问根域名服务器：“老大， 能告诉我www.baidu.com 的 IP 地址吗？” 根域名服务器是最高层次的，它不直接用于域名解析，但能指明一条道路。

3. 根 DNS 收到来自本地 DNS 的请求后，发现后置是 .com，说：“www.baidu.com 这个域名归 .com 区域管理”，我给你 .com 顶级域名服务器地址，你去问问它吧。”

4. 本地 DNS 收到顶级域名服务器的地址后，发起请求问“老二， 你能告诉我 www.百度.com  的 IP 地址吗？”

5. 顶级域名服务器说：“我给你负责 www.baidu.com 区域的授权 DNS 服务器的地址，你去问它应该能问到”。

6. 本地 DNS 于是转向问授权DNS 服务器：“老三，www.baidu.com对应的IP是啥呀？” baidu.com 的授权  DNS 服务器，它一定有baidu.com的地址。这个服务器是购买域名时，对应厂家的服务器，比如阿里云上买的域名，授权DNS服务器就是阿里云的某个DNS服务器。

7. 授权DNS服务器查询后把对应的IP地址告诉本地DNS。

8. 本地DNS再把IP地址返回给客户端，客户端拿着这个ip地址去访问对应的服务器。

这就是整个DNS的解析过程，来个直观一点的图吧：

![](../../\imgs\dns-3.jpg)

总之，DNS完成的是“指路”的任务，在这个寻找IP地址的过程中，我们注意：

客户端并没有去找根DNS服务器，客户端最多只到本地DNS服务器，如果本地DNS服务器没有查找到相关的信息，剩余的查询工作全部是本地DNS服务器完成查询，直到查询完毕，返回结果给客户端。

上图中我们也看到有13个根域名服务器，这些服务器以英文字母 A 到 M 依序命名，而且它们的 IP 地址基本不会变化，我们可以跟踪一下DNS解析过程：

```bash
[root@m01 ~]# dig +trace www.aliyun.com

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.8 <<>> +trace www.aliyun.com
;; global options: +cmd
# 这里是13台根服务器
. 131  IN NS e.root-servers.net.
. 131  IN NS f.root-servers.net.
. 131  IN NS j.root-servers.net.
. 131  IN NS a.root-servers.net.
. 131  IN NS i.root-servers.net.
. 131  IN NS l.root-servers.net.
. 131  IN NS k.root-servers.net.
. 131  IN NS d.root-servers.net.
. 131  IN NS b.root-servers.net.
. 131  IN NS g.root-servers.net.
. 131  IN NS m.root-servers.net.
. 131  IN NS h.root-servers.net.
. 131  IN NS c.root-servers.net.
;; Received 228 bytes from 223.5.5.5#53(223.5.5.5) in 30 ms

# 这里开始是gtld顶级域名服务器
com. 172800  IN NS b.gtld-servers.net.
com. 172800  IN NS i.gtld-servers.net.
com. 172800  IN NS e.gtld-servers.net.
com. 172800  IN NS h.gtld-servers.net.
com. 172800  IN NS k.gtld-servers.net.
com. 172800  IN NS g.gtld-servers.net.
com. 172800  IN NS a.gtld-servers.net.
com. 172800  IN NS c.gtld-servers.net.
com. 172800  IN NS j.gtld-servers.net.
com. 172800  IN NS l.gtld-servers.net.
com. 172800  IN NS m.gtld-servers.net.
com. 172800  IN NS d.gtld-servers.net.
com. 172800  IN NS f.gtld-servers.net.
com. 86400  IN DS 30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com. 86400  IN RRSIG DS 8 1 86400 20220306050000 20220221040000 9799 . mSH/a+D8mNSUXkh4BcIBC4qSSO8i7bEbj+Kzre8/4x8Dk2pSUWCmplI2 QSxK4toEtrfoIkZiA5uzjkdm83rJNhN6e9YoYKcLuw1Di2b2AylSBj1C LvvGf35SNWx6xUPa57dq8SJzJ93/Ml7VEA71p05vtO28cNBXpbtLv/BJ +oBS5vqFQW1nzOk5DM/B3mqjUzhYh9P2JV6NYHXo/avBF8+z5xI0YBhi ciQGdNaa3cNlzAPu39PMq8PkCl1Te1gF0bGHP6muwBYwN9xqutr9bKQR mWclvwirvEGrBsvLdHyw/cYZelpKUR6jxYA6jaJEWfiRCR+UMK8qIF+r IkmwFg==
;; Received 1174 bytes from 202.12.27.33#53(m.root-servers.net) in 61 ms

# 这里是aliyun.com的授权服务器
aliyun.com. 172800  IN NS ns3.aliyun.com.
aliyun.com. 172800  IN NS ns4.aliyun.com.
aliyun.com. 172800  IN NS ns5.aliyun.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0Q1GIN43N1ARRC9OSM6QPQR81H5M9A NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20220228052341 20220221041341 38535 com. kHFbWrb9PnX4/Qdvm6KX6kZUUqqlhn6LufPTw8Tiqc9NJulWenzJi/a0 Xvw4raIVEPPxhWnmFpwltc9mZMW/mVsXyr4xaXg2y1kBQASpRzLwf1Zf 4oaLmc6t6relhsVL+XLwR0DE4svATQNv+elAr84MM1LIwR4I+HwWn51z rL5Hs8ivlpVosi3AtfAb7sEkRNkxeYyk+IQlLYlwugqtIQ==
A0RKL5NVROI6OGM04U7CRPHUVLPDLVSK.com. 86400 IN NSEC3 1 1 0 - A0RM6AOP8AMDHR1FA4R1HF1NGALFU6A3 NS DS RRSIG
A0RKL5NVROI6OGM04U7CRPHUVLPDLVSK.com. 86400 IN RRSIG NSEC3 8 2 86400 20220228063117 20220221052117 38535 com. eOLjaVoGh4hEIVP5Ll1ikkwG7mEvjxmfiXNKOfH7YN41ssOvPktazfA/ Dxf8amzox0ij3q90FeJ/KAHCFX88deBwY/ArcwUoNMQaXdNT1efL6Dlw WZU5j5LD42UPR6NLcMW4hhuAzF3+5A/2QaWSbwvVLA+zHqpNoUL57RPS uxSgD7NfhFFEatY91dJg/Xk7sBEXIrmkYBTBopqt3inNCA==
;; Received 826 bytes from 192.26.92.30#53(c.gtld-servers.net) in 252 ms

# 拿到了aliyun的一个cname【其实跟指向IP地址是一样的，因为这个别名也要做一个A记录的】，
www.aliyun.com. 120  IN CNAME www-jp-de-intl-adns.aliyun.com.
;; Received 87 bytes from 140.205.122.133#53(ns4.aliyun.com) in 40 ms
```

> Tips：请思考，怎么确定上面dig命令是哪个软件包提供的。

## DNS常用记录类型

区域解析库文件：每个域名都有一个自己的区域解析库文件（zone文件），由一条条资源记录来定义具体指向

SOA记录：起始授权记录，一个区域解析库只能有一个SOA记录，而且必须为第一条。打个比方，建了个海岛，需要对外宣布世界上多了一个海岛，这个事情就是SOA在做 

NS记录：域名服务记录，一个区域解析库可以有多个NS记录，其中一个为主服务器。结合上面的例子，NS记录可以理解为用来宣布岛主是谁

A记录：地址记录，从域名解析为IP的记录

AAAA：IPv6的地址记录

PRT：反向解析记录，从IP解析为域名

MX：邮件交换记录。我们平时发邮件都是直接发到qq.com、163.com等域名上，但是这些域名肯定是有多台服务器的，MX记录就是指明了哪个服务器会负责邮件。MX服务可以存在多个，由优先级来区分先后，0-99，数字越小优先级越高

CNAME记录：给一个地址设置一个别名

## 推荐阅读

[一文彻底搞懂IP地址知识（值得收藏的手册）](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247496403&idx=1&sn=a30e17f821a0d606db9f583030692032&chksm=ce1669fdf961e0eb398efeae7b5c1f4e82fec3f483cc183757a35e8792cbd45e4a96aec57a06&scene=21#wechat_redirect)

[老司机：子网掩码我不怕，怕的是子网划分！](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247496441&idx=1&sn=e51c7a22c8fb9052d080ba3677489ae8&chksm=ce1669d7f961e0c19e9415a28b16c988390a15e502233b995de83460370d8021f676a68e9a36&scene=21#wechat_redirect)

[关于dhcp，看这一篇就够了！](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247496520&idx=1&sn=7cc2dc35a79d5f35f35167aa1848ade5&chksm=ce166866f961e1704cfa434ae3ff927634edf0e818b7c8d86d7edd7d03cba725dbb5308bf4f1&scene=21#wechat_redirect)

[网络配置net-tools工具包](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247496690&idx=1&sn=2d9c4259fddd641aaf70a328021ceb30&chksm=ce1668dcf961e1cac2fd8688520bcbb816e3a5d61e196fec047dfe8ba06795a8a046b01a9fb5&scene=21#wechat_redirect)

[ifconfig已淘汰，ip登场](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247496787&idx=1&sn=629f75a0b2c409ad337c2796c213cddf&chksm=ce166f7df961e66b703af872af63315211c38fc2508f2250b3368317aa136e511d51800383d5&scene=21#wechat_redirect)

[多网卡绑定之bond](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247497115&idx=1&sn=39b384e503be84664a9509eb472e026b&chksm=ce166eb5f961e7a3a3704422d6e3b8f8b593645a2b329b9241d984cdd0734c3f89ee3f9464d9&scene=21#wechat_redirect)

[多网卡绑定之team](http://mp.weixin.qq.com/s?__biz=Mzg2MTU4OTA1Nw==&mid=2247497128&idx=1&sn=d4f0b81a3ba67c7eaa26004526220460&chksm=ce166e86f961e7905ef09a293f3cd255739c8fa10da4d074079df2d981310ed43da1d5186757&scene=21#wechat_redirect)
