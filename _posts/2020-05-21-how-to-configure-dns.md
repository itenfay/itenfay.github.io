---
layout: post
title: "手机访问网络特慢且有时无法加载内容，配置好 DNS 完美解决"
author: "Tenfay"
date: 2020-05-21
tag: iOS
---

![](https://itenfay.github.io/images/dns/bgp_anycast.png)

## DNS 是什么?

DNS 简单说就是把你能看懂的域名(例如：jianshu.com)转换成对应的 ip 地址，如果没有 DNS，我们就找不到服务器，网络中连接和传输都是通过 ip+port 的方式确定一个资源的位置。所以，我们输入域名，实际上网络并不认识它，这里就需要 DNS 服务器帮忙把域名兑换成 ip。


## DNS 作用是什么？

使用公共 DNS 解析服务后，让网上冲浪更加稳定、快速、安全； 为家庭拦截钓鱼网站，过滤非法网站，建立一个绿色健康的网上环境；为域名拼写自动纠错，让上网更方便。

**推荐以下公共 DNS:**

| 服务商 | IPv4 | IPv6 | 地址 |
| :--- | :--- | :--- | :---: |
| 阿里公共DNS | 223.5.5.5 <br> 223.6.6.6 | 2400:3200::1 <br> 2400:3200:baba::1 | [链接](https://www.alidns.com) |
| 腾讯公共DNS | 119.29.29.29 <br> 182.254.116.116 | | [链接](https://www.dnspod.cn/Products/Public.DNS) |
| 百度公共DNS | 180.76.76.76 | 2400:da00::6666 | [链接](https://dudns.baidu.com/intro/publicdns/) |
| 114 DNS | 114.114.114.114 <br> 114.114.115.115 <br><br> 114.114.114.119 <br> 114.114.115.119 <br><br> 114.114.114.110 <br> 114.114.115.110 | | [链接](http://www.114dns.com) |
| Google | 8.8.8.8 <br> 8.8.4.4 | | [链接](https://developers.google.com/speed/public-dns/) |
| Cloudflare | 1.1.1.1 <br> 1.0.0.1 | 2606:4700:4700::1111 <br> 2606:4700:4700::1001 | [链接](https://1.1.1.1/) |
| IBM Quad9 | 9.9.9.9 <br> 149.112.112.112 | 2620:fe::fe <br> 2620:fe::9 | [链接](https://www.quad9.net/) |
| OpenDNS | 208.67.222.222 <br> 208.67.220.220 | | [链接](https://www.opendns.com/) |
| CNNIC sDNS | 1.2.4.8 <br> 210.2.4.8 |  | [链接](https://www.sdns.cn) |
| OneDNS | 117.50.10.10 <br> 52.80.52.52 <br><br> 117.50.11.11 <br> 52.80.66.66 | | [链接](https://www.onedns.net/) |
| DNS派 | 电信：首选：101.226.4.6 <br> 联通：首选：123.125.81.6 <br> 移动：首选：101.226.4.6 <br> 铁通：首选：101.226.4.6 | | [链接](http://www.dnspai.com/public.html) |
| yandex | 77.88.8.8 <br> 77.88.8.1 | | [链接](https://dns.yandex.com/advanced/) |

各家的解析速度和地区都是有差别的，各家宣传的理念也有些区别，但都是做域名解析 ip 的，有的宣传，稳定，快速，有的无劫持，安全稳定！选择适合自己的线路和自己认为干净的就可以！ 有时候，你会遇到，突然某个网站不能访问，但换了dns 就可以，这就是你原来的 dns，对你访问的域名不能解析啦，导致访问失效。换了另外家，还保有这个域名的解析，就通啦！


## 如何配置 DNS ？

- [苹果手机怎么配置 DNS](https://link.jianshu.com?t=https://jingyan.baidu.com/article/6079ad0ed5214628ff86dbb5.html)
- [安卓手机如何修改 DNS](https://link.jianshu.com?t=https://jingyan.baidu.com/article/86fae346e7b0303c49121a2a.html)
- [Mac 系统 DNS 怎样设置](https://link.jianshu.com?t=https://jingyan.baidu.com/article/4dc40848081204c8d946f184.html)
- [Win10 怎么修改 DNS](https://link.jianshu.com?t=https://jingyan.baidu.com/article/2fb0ba40833b0a00f2ec5f28.html)
