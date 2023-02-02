# 主从DNS部署

安装dns软件：

```bash
yum -y install bind
```

### 配置文件解析

/etc/named.conf这个配置文件主要分3段内容,options是全局配置，logging是日志配置，最后是区域解析库配置以及所包含的区域解析库文件配置。

```js
# vim /etc/named.conf
options {
        listen-on port 53 { 10.0.0.62; };
        listen-on-v6 port 53 { ::1; };
        directory "/var/named";
        dump-file "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file "/var/named/data/named.recursing";
        secroots-file "/var/named/data/named.secroots";
        allow-query { any; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.root.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

//.表示根区域,这个区域是必须要有的
zone "." IN {
        type hint; //指定根区域类型。
        file "named.ca"; //指定根区域解析库文件名称所在位置，相对于/var/named
};

// 指定区域文件名称，所有的区域定义都可以写在named.rfc1912.zones文件中
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

**配置文件详解：**

listen-on port 53 { 10.0.0.62; };表示监听的端口和IP地址，其中{}内部两端都要有空格，IP地址要以;号结尾。

directory指定named的工作目录，后面的域配置文件、日志文件都存放在这里。例如上面示例中的named.ca就是保存在/var/named中

allow-query表示允许哪些主机可以访问当前dns服务，改成any表示任何主机都可以访问当前的dns服务

recursion如果你打算构建一个递归（缓存）DNS服务器，那这里就要设置为yes；如果是构建权威服务器，那这里就应该设置为off。

zone用于定义DNS服务器所服务的区域，其中包括区域名、区域类型和区域文件名等信息。

> DNS区域分为两大类:正向查找区域和反向查找区域。
> 
> 正向查找区域用于FQDN（主机名+域名）到IP地址的映射，当DNS客户端请求解析某个FQDN时，DNS服务器在正向查找区域中进行查找，并返回给DNS客户端对应的IP地址;
> 
> 反向查找区域用于IP地址到FQDN的映射，当DNS客户端请求解析某个IP地址时，DNS服务器在反向查找区域中进行查找，并返回给DNS客户端对应的FQDN。

file：指定区域数据库文件的名称，应在文件名两边使用双引号。这里只写文件名，对应的文件保存在哪呢？其实它的保存位置是由options部分的directory选项指定的，这个选项默认是/var/named。

type：指定区域类型。有 hint、master、slave 和 forward 等类型，分别表示根区域、主区域、辅助区域和转发区域。hint就是交给根的意思，说明根区域类型是缓冲服务器。

#### 递归DNS服务器、权威DNS服务器

1.递归服务器

当客户端发起上网请求时，在查询了本机各种缓存之后没有获得相应的解析记录，就会向本地DNS服务器发起查询请求。

本地DNS服务器会先查询自己的本地缓存，如果有结果直接返给客户端，如果没有结果就会代替客户端向根DNS服务器、顶级域名服务器、二级域名服务器等一级一级递归查询下去，最终找到域名对应的权威服务器取得结果并返回给客户端，同时将记录保存到本地缓存中。

小结：在整个DNS查询过程中，客户端除了在一开始向本地域名服务器（或者叫本地DNS服务器）发起请求外，其余时间都是由本地域名服务器代替进行递归查询。这里的本地域名服务器就是递归DNS服务器。

2.权威服务器

负责最终解析域名的服务器就是权威服务器。权威服务器与递归服务器不同，它不负责帮助客户端进行递归查询返回解析记录，它本身的用途就是对域名进行解析。

10T 技术限时资源大放送！包括但不限于：Linux、虚拟化、容器、云计算、网络、Python、Go 等。在开源Linux公众号内回复「10T」，即可免费获取！

每个特定的域名，权威DNS服务器可能并不相同。这种权威DNS服务器只对自己所拥有的域名进行域名解析，对于自己不负责域名则无法进行解析。比如递归DNS去taobao.com的权威DNS服务器查询baidu.com的域名肯定会查询失败。

因此递归解析服务器是运营商搭建，帮助所属网络用户去响应的权威DNS服务器查询解析结果。而权威解析服务器一般是企业自建或域名服务商建设，给购买域名的企业和个人使用，方便其对域名进行解析管理。

### 配置主服务器

```js
# vim /etc/named.conf
options {
        listen-on port 53 { 10.0.0.62; }; //主服务器的地址
        listen-on-v6 port 53 { ::1; };
        directory "/var/named";
        dump-file "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file "/var/named/data/named.recursing";
        secroots-file "/var/named/data/named.secroots";
        allow-query { any; }; //允许任何主机可以访问本DNS服务

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes; //配置成递归DNS服务器

        dnssec-enable yes;
        dnssec-validation yes;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.root.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";


# 在/etc/named.rfc1912.zones文件末尾添加正向解析区域和反向解析区域
[root@slave1 ~]# vim /etc/named.rfc1912.zones
......
// 在文件最后添加一个正向解析区域,并定义为主服务器
zone "test1.com" IN {
    type master; //master表示主服务器
    file "test1.com.zone"; //指明区域解析库文件，相对于/var/named/
};

//在文件最后添加一个反向解析区域，并定义为主服务器
zone "0.0.10.in-addr.arpa" IN {
    type master;
    file "test1.com.local";
};
```

创建test1.com.zone解析库文件

```bash
# 复制模板来创建区域文件
[root@slave1 ~]# cd /var/named/

# 注意：复制的时候要用-a选项，以便保持用户拥有者等属性信息，否则named程序无法解析。当然你也可以手动修改属性，使复制后的文件的拥有者和所属组都是named
[root@slave1 named]# cp -a named.localhost test1.com.zone
[root@slave1 named]# cp -a named.loopback test1.com.local

# 设置正向解析
[root@slave1 named]# cat test1.com.zone
$TTL 1D
@ IN SOA @ rname.invalid. (
          0  ; serial
          1D ; refresh
          1H ; retry
          1W ; expire
          3H ) ; minimum
  NS @
  A 10.0.0.62
www IN A 10.0.0.62
ftp IN A 10.0.0.62
mail IN CNAME www

# 设置反向解析
[root@slave1 named]# cat test1.com.local
$TTL 1D
@ IN SOA test1.com. rname.invalid. (
          0  ; serial
          1D ; refresh
          1H ; retry
          1W ; expire
          3H ) ; minimum
  NS test1.com.
  A 10.0.0.62
10 IN PTR www.test1.com.
11 IN PTR ftp.test1.com.

# 重新配置本机的DNS：
[root@slave2 ~]# cat /etc/sysconfig/network-scripts/ifcfg-ens33
......
IPADDR=10.0.0.62
PREFIX=24
GATEWAY=10.0.0.2
DNS1=10.0.0.62
```

启动named服务：

```bash
[root@slave1 ~]# systemctl start named

# 测试一下
[root@slave1 ~]# nslookup www.test1.com
Server: 10.0.0.62
Address: 10.0.0.62#53

Name: www.test1.com
Address: 10.0.0.62
[root@slave1 named]# nslookup ftp.test1.com
Server: 10.0.0.62
Address: 10.0.0.62#53

Name: ftp.test1.com
Address: 10.0.0.62
```

### 配置从服务器

```bash
# 修改主配置文件，只改两行，其余保持默认即可
[root@slave2 ~]# cat /etc/named.conf
......
options {
  listen-on port 53 { 10.0.0.63; }; # ip地址改成本地地址
  listen-on-v6 port 53 { ::1; };
  directory "/var/named";
  dump-file "/var/named/data/cache_dump.db";
  statistics-file "/var/named/data/named_stats.txt";
  memstatistics-file "/var/named/data/named_mem_stats.txt";
  recursing-file "/var/named/data/named.recursing";
  secroots-file "/var/named/data/named.secroots";
  allow-query { any; }; # 任何主机都可以访问本地dns服务
......


# 在/etc/named.rfc1912.zones文件末尾添加正向解析区域和反向解析区域
[root@slave2 ~]# vim /etc/named.rfc1912.zones
......
zone "test1.com" IN {
        type slave;
        masters { 10.0.0.62; };
        allow-notify { 10.0.0.62; };
        file "slaves/test1.com.zone";
};

zone "0.0.10.in-addr.arpa" IN {
        type slave;
        masters { 10.0.0.62; };
        allow-notify { 10.0.0.62; };
        file "slaves/test1.com.local";
};
```

注意事项：从服务器的区域解析库文件应当是从主服务器加载过来的，所以无需创建区域解析库文件。微信搜索公众号：信安黑客技术，回复：黑客 领取资料 。

启动named服务：

```bash
[root@slave2 ~]# systemctl start named

# 重新配置本机的DNS：
[root@slave2 ~]# cat /etc/sysconfig/network-scripts/ifcfg-ens33
......
IPADDR=10.0.0.63
PREFIX=24
GATEWAY=10.0.0.2
DNS1=10.0.0.63

# 启动以后，会自动生成区域解析库文件，这两个文件来源于主服务器
[root@slave2 ~]# ll /var/named/slaves/
total 8
-rw-r--r-- 1 named named 336 Sep 5 20:02 test1.com.local
-rw-r--r-- 1 named named 319 Sep 5 20:02 test1.com.zone

# 测试验证
[root@slave2 ~]# nslookup www.test1.com
Server: 10.0.0.63
Address: 10.0.0.63#53

Name: www.test1.com
Address: 10.0.0.62

[root@slave2 ~]# nslookup ftp.test1.com
Server: 10.0.0.63
Address: 10.0.0.63#53

Name: ftp.test1.com
Address: 10.0.0.62
```

### 客户端验证

```bash
# 配置DNS：客户端需要配置多个dns，我们这里配置主从dns的地址
[root@master ~]# cat /etc/sysconfig/network-scripts/ifcfg-ens33
......
IPADDR=10.0.0.61
PREFIX=24
GATEWAY=10.0.0.2
DNS1=10.0.0.62    
DNS2=10.0.0.63

# 重启网络使配置生效
[root@master ~]# systemctl restart network

[root@master ~]# nslookup www.test1.com
Server: 10.0.0.62
Address: 10.0.0.62#53

Name: www.test1.com
Address: 10.0.0.62

[root@master ~]# nslookup ftp.test1.com
Server: 10.0.0.62
Address: 10.0.0.62#53

Name: ftp.test1.com
Address: 10.0.0.62
```

主从一同工作，在客户端会配有多个DNS地址，如果第一个DNS地址无法解析，那么第二DNS地址也是无法解析的，当第一个DNS地址联系不上时（宕机）才会联系第二个DNS地址并寻求解析。

故障测试

```bash
# 停止master DNS
[root@slave1 ~]# systemctl stop named

# 到客户端上去验证
[root@master ~]# nslookup www.test1.com
Server: 10.0.0.63
Address: 10.0.0.63#53

Name: www.test1.com
Address: 10.0.0.62
```

通过上面的实验我们可以发现，停掉master DNS服务以后，客户端还是可以继续解析到域名， 即从DNS已经开始启用了。
