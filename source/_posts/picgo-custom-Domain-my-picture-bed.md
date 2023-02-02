---
title: picgo + custom Domain = my picture bed(搭建自己的图床)
date: 2023-01-04 15:43:16
tags: 图床 COS CDN
featured_image: https://www.bythewayer.com/img/txcos.webp
---

之前用的第三方云图床，发现很多时候还是加载太慢，所以干脆自己搭建一个图床。
本想搭建一个免费的基于 github 的图床，但看到腾讯云 COS 支持图片存储，本着试一试的心态，从头开始搭建自己的图床，最终的效果就是：

访问图片明显快多了：
![cos_1](https://www.bythewayer.com/img/cos_1.webp)

下面简单说说搭建步骤，其中里面会夹杂 COS/CDN/CNAME 别名解析等专业术语，

- COS: 对象存储（Cloud Object Storage，COS）是由腾讯云推出的无目录层次结构、无数据格式限制，可容纳海量数据且支持 HTTP/HTTPS 协议访问的分布式存储服务
- CDN: 内容分发网络（Content Delivery Network，CDN），是在现有 Internet 中增加的一层新的网络架构，由遍布全球的高性能加速节点构成。这些高性能的服务节点都会按照一定的缓存策略存储您的业务内容，当您的用户向您的某一业务内容发起请求时，请求会被调度至最接近用户的服务节点，直接由服务节点快速响应，有效降低用户访问延迟，提升可用性。
- CNAME: 将域名指向另一个域名地址，与其保持相同解析，如 https://www.dnspod.cn
  www:常见主机记录，将域名解析为 www.bythewayer.com
  @:直接解析主域名 bythewayer.com
  mail:将域名解析为 mail.bythewayer.com，通常用于邮件服务
  ![CNAME 记录](https://www.bythewayer.com/img/cos_6.webp)

### First 下载 PICGO

选择适合的自己的版本[PicGo](https://github.com/Molunerfinn/PicGo/releases/tag/v2.3.1)安装即可

### Second 购买 COS 服务，新人有足够的优化

[COS](https://console.cloud.tencent.com/cos),能打开之后，开始创建自己的存储桶,创建完之后：
![cos_2](https://www.bythewayer.com/img/cos_2.webp)

### Third PicGo 配置

![PicGo 配置](https://www.bythewayer.com/img/cos_3.webp)

总共需要配置的有：
1.secretId/secretKey/Appid 这三个参数值在: [API 密钥管理](https://console.cloud.tencent.com/cam/capi)
![密钥管理](https://www.bythewayer.com/img/cos_4.webp)
2.Bucket 值是：存储桶名称

3.存储区域： ap-nanjing(以存储桶那里的信息为准，我这边填的是南京)

4.设置自定义域名： https://www.bythewayer.com，这个域名是我配置了自定义 CDN 加速域名，这样的好处就是后面上传的图片路径就是 自定义域名+路径+图片名称

配置完成之后就能上传图片，上传到的图片能在 COS 文件列表看到

### 关于配置 CDN 加速域名

域名需要提前备案好，否则不支持 CDN 加速，源站类型我这里原则的事静态网站源站，这个静态网站源站也是在当前存储桶里设置的，文件也就是放在存储桶下的跟路径里
![静态网站源站文件](https://www.bythewayer.com/img/cos_5.webp)

> 有一个问题就是如何自动传输代码文件，不可能每次都需要手动更改

启用 CDN 域名加速之后，如何验证？

如果网站没有开启 CDN，不同地区 Ping 网址是网站服务器的真实地址，如果开启了 CDN 加速，网站内容会缓存到各地区离你最近的服务器，所以访问 IP 会发生变化，根据这个原理，就很容易判断网站是否开启了 CDN 了。
推荐网站测试工具二：[站长工具 Ping](https://ping.chinaz.com/bythewayer.com)
