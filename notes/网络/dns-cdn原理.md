# dns-cdn原理

DNS 的流程是会先查找浏览器 DNS 缓存、hosts 文件、系统 DNS 缓存，然后请求本地 DNS 服务器，由它去一级级查询最终的 IP：

![](../../\images\dns-and-cdn-1.png)

CDN 是基于 DNS 的，在权威域名服务器做了 CNAME 的转发，然后根据请求 IP 的所在地来返回就近区域的服务器的 IP。

![](../../\images\dns-and-cdn-2.png)
