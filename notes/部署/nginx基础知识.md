# nginx基础知识

### Nginx是什么

首先**Nginx**是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。其是一款**轻量级的web服务器**、**反向代理服务器**、**电子邮件服务器**，实际在在设计之初**Nginx**的产品目的就是为了邮件服务而诞生的，特点是占有内存少、体积小、并发能力强、性能高，在日常开发过程中，很多场景下都使用到了他。

### Nginx有什么用

从大的地方来看，总结下来有三个特点：**反向代理**、**负载均衡**、**动静分离**

#### 反向代理

反向代理是**Nginx**使用最多的一种场景了，假如我们开发的一个服务请求量较大，一台服务器已经无法承载了，我们需要扩容使用多台服务器，那么这个时候我么就需要加一层**中间件**来分配用户的请求，把用户的请求下发到不同的服务器，这个时候我们就可以使用**Nginx**来完成，当然这只是他的一个作用，在了解反向代理之前我们再来说说**正向代理**，有反必然有正。

先说说反向代理，假设我们是一个用户，在上述场景中，不然增加多少台服务器，我们用户始终访问一个相同的域名，对于用户而言，其做的反向代理对用户是无感知的，这里的代理是**Nginx**中间件这一层把用户的请求代理到了我们的服务器上，同时他也是在服务端完成的，**反向代理的过程，隐藏了真实的客户端。客户端请求的服务都被代理服务器代替来请求。**

再说说正向代理，假设这样一个场景，在我们日常开发中应该经常会使用到vpn吧，公司的一些项目或者私有git或者一些内部的网站，我么需要登录vpn才能访问，其基本原理呢就是一个位于客户端和原始服务器之前的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并且指定目标（原始服务器），然后代理向原始服务器转交请求并将获得的内容返回给客户端，客户端才能使用正向代理，我们登录vpn之后所有的请求将会通过vpn的这台服务器代理客户去访问某些资源或者网站，那么这台代理服务器将会是公司内部的白名单ip，这样所有的请求都统一从这里进入，就实现了一个正向代理。如此一来呢，**正向代理的过程呢就隐藏了真实的客户端，客户端请求的服务都被代理服务器代替来请求。**

****本质上代理服务器还是那个代理服务器,如果替客户端干活就是正向代理,如果替服务端干活就是反向代理;****

![](../../\imgs\nginx-basic-1.png)

#### 隐藏服务端口号

首先呢，我们有了自己的一台服务器后，大概率你也会有自己的域名，这个时候，我们假如有多个网站，那么大概率你会去创建自己的二级域名，这个时候我们想要部署多个网站的时候，很可能变成了**192.xx.xx.xx:888[1]**、 **192.xx.xx.xx:999[2]**  这样，假如你是一个前端，还部署了多个Node这样的项目的时候，那么你肯定会遇到这样的问题，我们知道一个服务器，正常每一个端口都是单独的，不能共享【也有特殊场景，比如socketio提供的双工通讯可以让http和ws使用同一个端口】，我们想要通过二级域名去区分不同的服务就需要将其解析到不同的二级域名中，我们知道默认打开一个网站的时候不加端口的情况下、**http对应80端口**、**https对应443端口**，正常情况下云厂商也都是默认开启这两个端口的，所以我们在使用**Nginx**的时候，只需要监听**80和443端口**，判断访问的域名地址，将其转发到不同的端口下面（具体配置最后放上），那么我们作为一个个人用户，就可以在这种场景下实现不需要加上难看的端口号了，所有的请求都会通过**80、443**代理到我们真实的服务端口。

#### 部署前端项目

因为其也是一个静态web服务器，部署我们前端的项目非常简单，相信很多人如果尝试自己去部署，使用**Nginx**是十分简单的，下载，安装，运行，指定默认目录，前端项目拖进去就能访问了，这个步骤比较简单，就不过多说明了，这也是我们前端可以用到的一种场景。

#### 负载均衡

说完反向代理，我们再来看看负载均衡就很好理解了，在上述场景中，假如我们使用了三台服务器，客户端发送来请求之后我们把请求发给谁呢，总不能虽然有三台服务器，我们都转给其中一台吧，明显很不合理，为了保证我们的性能最大化，负载均衡的意思呢就是会合理的把客户端的请求分配给服务器，我们默认呢就等比例分配，可以理解为1:1:1，但是实际场景中，我们的服务器配置可能不同，可能有好有坏，所以这里有一个权重的概率，可以有我们自己配置哪台服务器多分配请求，哪一台少一点请求，这样的配置可以让服务器的性能得到最大化，合理分配请求将会使系统更为健壮稳固。

#### 动静分离

在我们的开发过程中呢，有的请求是需要动态的计算的，比如需要连接**mysql**数据库去增删查改，这些属于**动**，而对于前端的一些文件**html、css、js**等这些文件呢，实际并不需要后台处理，那么我们就可以通过**动静分离的模式将其区分开**，就可以将这些静态文件进行缓存操作，这样就可以提高效率了，当然在现在**前后端分离的场景下**这一点的实用性目前对我个人而言似乎还并不高，使用场景相对上面两点比较有限，这三点是其比较大的三点，小的地方还有很多细节，我们来通过配置来具体看看吧

### Nginx实战分析

在要使用前我们需要先进行下载，下载呢非常简单，如果您是*windows*，那么前去官网下载解压直接打开*exe*即可，*mac*的用户大多都有**brew包管理**，我们只需要`brew search nginx`一行即可下载完成。

使用呢非常简单，只需要在终端输入`nginx`即可启动成功，他默认使用的是**8080端口**，所以我们启动成功后访问**127.0.0.1:8080**就可以看到经典的**welcome nginx**了，首先看到这个页面的时候我们作为前端的第一印象是什么呢，这个地址显示了一个网页，那么肯定这个网页是他自带的，其此，如果我们改了内容是不是就会显示自己的东西了呢，再者那么我们把自己的项目替换成他的这个文件是不是就可以访问我们自己项目了呢，当然如此，前端的很多项目部署其实非常简单，我们把自己项目拖到这个文件的地址就覆盖掉他了，但是我们怎么知道这个文件在什么目录，什么地址呢，那我们就得看看配置信息了。

在终端输入 `nginx -t`就可以显示具体信息了

```bash
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
```

这样我们可以看到**nginx的配置文件地址已经当前的配置文件是否合规可以使用**如果你的配置文件有错误，这里也会提示你，那么我们可以**cd到配置目录**然后看看它的默认配置，我们可以直接`open .`或者`cat nginx.conf`都可以查看默认配置，如下，

```nginx
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
    #access_log  logs/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    #keepalive_timeout  0;
    keepalive_timeout  65;
    #gzip  on
    server {
        listen       8080;
        server_name  localhost;
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        location / {
            root   html;
            index  index.html index.htm;
        }
        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}
```

这里的配置太多了，为了简单点，删除了一部分我们暂时不需要的配置，然后我们来分析一下

#### 监听端口8080

首先我们知道其默认端口是**8080**同时呢我们刚也说了，浏览器只有**80和443端口才是不需要我们拼接的**，所以正常情况我们也不想要端口号，所以，我们就只需要找到**8080将其替换成80即可**，所以我们现在就知道了**listen**这个配置就是指我们要监听哪个端口号，改为**80之后**我们访问服务就不需要再加端口了，可以直接访问**127.0.0.1**即可，当然我们现在访问是不可以的，**因为Nginx修改完配置之后需要重载**，我们可以停止再启动，或者更简单的`nginx -s reload`重载即可，具体的更多命令我们后面再说，重载完成之后我们访问就会发现是同样的效果，如果是在我们的服务器上，安装这里就不说了，去官网或者随便百度都可以找到，我们把域名解析到自己服务器，这个时候访问自己的域名不加端口就能访问到项目了，如果你还是个初学者，不太会**linux**的一系列命令，那么我推荐你使用**xshell**和**sftp**使用可视化工具直接对文件进行拖拽操作即可完成上述步骤，那么第一步我们的网站其实这样就可以部署完成了，如果你没有域名，那么直接输入你的**服务器ip**同样可以访问成功。

#### 如何一台服务器部署多个网站

作为我们个人开发者来说，我们大多数情况下只有一个服务器，但是我们还想部署多个项目，这个时候该怎么办呢，我们就需要下一项配置呢，为了让很多同学更方便理解，我们以前端来举例，我们在自己电脑下开发项目的时候，我们同时启动了前端项目假如在**8080端口**，同时启动了后端**node项目的3000**端口，那么我们是不是就可以通过两个端口启动了两个服务，那么在我们的服务端也是同理，多个服务只需要运行在不同端口即可，在**Nginx**的配置中，我们只需要根据用户访问的地址来指向不同的端口就行，那么这个怎么配置呢，以我个人的网站来一段基本配置：

```nginx
server {
    listen       80;
    server_name   chat.jiangly.com;
    location / {
        proxy_pass   http://127.0.0.1:7000;
    }   
}
```

我们发现如此简单，两行即可，监听80端口，当访问的域名是**chat.jiangly.com**的时候指向我们的7000端口，那么7000端口就是我们项目运行的真实端口，很明显呢这是一个后端**node**服务，这时候小伙伴有疑问了，如果我们是前端静态项目怎么办，我打了包了啊，好像没有端口了，直接访问了啊，那我哪来的这个**proxy_pass呢**，也很简单：

```nginx
server {
        listen 80;
        server_name project1.jaingly.com;

        location / { 
                root /data/project1/;
                index index.html;
        }
}
```

我们发现**proxy_pass**变成了**root**，**root**呢指向了一个地址，那么我们就把自己项目放这个地址就行了，原来前端的项目这么简单啊，这一下，我们就可以一下部署好多网站啦，既可以访问前端这种静态网站，也可以配置**node项目这种带端口的啦**，这下服务器想部署多少项目不是都可以了么。如果小伙伴没有域名的话呢 **server_name**就需要变成**127.0.0.1加上端口号或者项目地址**了，比如**127.0.0.1:7000**、**127.0.0.1/project1**这样来区分项目。同时需要注意的是，这里使用的是相对路径**是以我们nginx -t那里的配置文件nginx.conf那一层为对比的路径**，记住地址别放错了文件，否则找不到哟

#### 使用Https服务

这个时候有兴趣的同学发现，那我这个没有**https认证好丑啊**，还提示不安全，我也想要一个**Https**，那我们怎么办呢，前面说到，**Https对应的是443的端口**，那我们直接监听443不就可以了么，在默认配置中，我们看见有监听**443端口但是注释了**，我们之间打开，配置和**80**完全一样没有区别，我们直接三下五初二，保存，重载，发现，咦报错了，原来说我们没有证书啊，没错，**Https是需要证书的**，这个证书呢需要收费，但是作为卑微的开发仔当然不愿意去花这个钱了呗，我个人呢是使用的**腾讯云**,小伙伴们可能使用的是其他厂商，但是肯定都会提供免费的证书供大家使用，**去到域名管理的地方**找到申请免费的**Https证书**，**腾讯云**在我个人使用每次申请最多一分钟非常的快，完了之后下载会发现里面有很多的格式。我么要下载**Nginx**的格式，下载之后呢，打开里面会有很多文件，但我们只需要两个，**crt格式的和key格式的**，然后我先放一个个人的基本配置

```nginx
server {
    listen 443 ssl;
    server_name   chat.jiangly.com;
    ssl_certificate conf.d/chat.jiangly.com_ssl/1_chat.jiangly.com_bundle.crt; 
    ssl_certificate_key conf.d/chat.jiangly.com_ssl/2_chat.jiangly.com.key; 
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
    ssl_prefer_server_ciphers on;
    location / {
        proxy_pass   http://127.0.0.1:7000;
        root html; 
        index  index.html index.htm;
    }       
}
```

别的地方都是一样的我们不用关心，我们看到两个配置项，**ssl_certificate_key**、**ssl_certificate**后面分别对应了两个地址，那么这个地址呢就是我们刚下载的两个文件的地址，/对应的相对地址呢和上面相同，只需要配置这两个文件，其他地方都不用改变，然后重载，这个时候就可以访问**https服务了**，这样一看我们的网站就很正规了呢，我们通过这么几个基础功能就实现了自己的网站部署，并且可以多网站、多域名、没端口、带证书等等功能，当然这还不够，我们还可以做更多的事情

#### Websocket服务

我们去开发项目的时候可能会经常使用到**websocket**服务，如果还是这样的配置呢，我们会发现，请求被拦截掉了，这是为什么呢？其实是因为我们使用**ws服务**的时候需要告诉**nginx**我们需要对协议进行升级，所以当你遇到这个问题的时候只需要增加两行配置即可

```nginx
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

如果遇到这个问题，加上重载即可。

#### server匹配规则

我们上面看到我们可以写很多组的**server**，那么**Nginx**是从上往下匹配的，当匹配到第一个之后就会立即退出，所以如果你遇到当你的配置怎么也不生效的时候，先看看是不是在上面被其他规则匹配到了。

#### history路由模式、跨域、缓存、反向代理

当我们开发的前端项目例如**vue**时，路由通常会有两个选项，一个带**#**的哈希路由一个不带，同时文档中还说了我们要使用，**history**就需要后端去改配置，实际这个在**nginx**就是简简单单的几行而已，我们只需要添加下面这几行，就可以使用**history路由了**，同时还有设置一些静态格式文件的缓存，设置跨域访问等等都是简单的配置就可以了

```nginx
# html设置history模式
location / {
    index index.html index.htm;
    proxy_set_header Host $host;
    # history模式最重要就是这里
    try_files $uri $uri/ /index.html;
    # index.html文件不可以设置强缓存 设置协商缓存即可
    add_header Cache-Control 'no-cache, must-revalidate, proxy-revalidate, max-age=0';
}

# 接口反向代理
location ^~ /api/ {
    # 跨域处理 设置头部域名
    add_header Access-Control-Allow-Origin *;
    # 跨域处理 设置头部方法
    add_header Access-Control-Allow-Methods 'GET,POST,DELETE,OPTIONS,HEAD';
    # 改写路径
    rewrite ^/api/(.*)$ /$1 break;
    # 反向代理
    proxy_pass http://static_env;
    proxy_set_header Host $http_host;
}

location ~* .(?:css(.map)?|js(.map)?|gif|svg|jfif|ico|cur|heic|webp|tiff?|mp3|m4a|aac|ogg|midi?|wav|mp4|mov|webm|mpe?g|avi|ogv|flv|wmv)$ {
    # 静态资源设置七天强缓存
    expires 7d;
    access_log off;
}
```

#### 负载均衡

我们可以基于**upstream模块**来做负载均衡，也就是设置权重以及配置地址

```nginx
upstream backserver{ 
    # 哈希算法，自动定位到该服务器 保证唯一ip定位到同一部机器 用于解决session登录态的问题
    ip_hash; 
    server 127.0.0.1:9090 down; (down 表示单前的server暂时不参与负载) 
    server 127.0.0.1:8080 weight=2; (weight 默认为1.weight越大，负载的权重就越大) 
    server 127.0.0.1:6060; 
    server 127.0.0.1:7070 backup; (其它所有的非backup机器down或者忙的时候，请求backup机器) 
}
```

我们可以看到四个服务都是我们本机，正式的生产场景中呢，我们其实应该是别的服务器的**ip地址**，一般**nginx会独立部署到一台服务器**，其他的服务会部署在其他服务器，所以在这样的场景下，我们需要这多台服务器在一个内网环境中，否则如果走公网，那么就会白白增加耗时，这里是我们需要了解的点。

上述场景呢是我为大家总结的一些常用场景，其实**Nginx**还有其他许许多多的更细致的功能，例如，压缩，服务错误的优雅降级等等功能，如果有兴趣或者真实需要用到的时候再去查阅文档吧，毕竟这玩意儿记太多配置可就有点占用咱们大脑的宝贵内存了。

### 常用指令

说了上面那么多了，为大家总结几个常用指令供大家使用吧

| 指令              | 解释                  |
| --------------- | ------------------- |
| nginx -s reopen | 重启Nginx             |
| nginx -s reload | 重新加载配置文件，优雅重启 推荐使用  |
| nginx -s stop   | 强制停止                |
| nginx -s quit   | 安全退出                |
| nginx -t        | 检测配置文件地址 以及检测配置是否正常 |
| nginx -v        | 显示版本信息并退出           |
| killall nginx   | 杀死所有nginx进程         |

上述呢就是一些常用的基本命令了，更多命令呢平时用的较少了，需要再去查询吧，大部署的后端指令呢都是 -s 表示发射命令的意思，记住这些常用命令就足够我们日常使用了。
