---
title: 虚假的https与真正的SaaS优选
published: 2026-05-01
description: 我终于有https啦
image: ""
tags: []
category: ""
draft: false
lang: ""
---
# 起因
有了这个博客以后, 就一直在意到底有没有人看这个博客, 又有多少人看, 所以有一个访问量追踪的东西就好了.
## Umami
然后我就知道了这个, Acofork也发过一些关于umami的文章, 关于[部署](https://blog.2x.nz/posts/umami/)和[迁移](https://blog.2x.nz/posts/umami-migration/)甚至还有直接部署到[EO云函数](https://blog.2x.nz/posts/eo-umami/)的方法. 不过我还是决定以最朴素最灵活的本地部署了.

具体部署的过程直接看[官方文档](https://docs.umami.is/docs/install)就行, 讲几个小坑:
- nvm的镜像, `export NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node`
- PostgreSQL, 下好之后`sudo -u postgres psql`打开数据库终端, 和mySQL有一定区别
- 默认账号密码是admin和umami
顺便贴一个claude写的systemd吧, 把路径啥的改改再用:
```ini
[Unit]
Description=Umami Analytics
After=network.target postgresql.service

[Service]
Type=simple
User=root
WorkingDirectory=/home/umami
ExecStart=/root/.nvm/versions/node/v24.15.0/bin/pnpm start
Restart=always
RestartSec=5
EnvironmentFile=/home/umami/.env
Environment=PATH=/root/.nvm/versions/node/v24.15.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
[Install]
WantedBy=multi-user.target
```

但是我加完tracker就发现问题了, umami里怎么没有记录啊, 打开F12一看原来是博客是https的, 里面link的script也得是https, 那我的服务器显然是没有SSL证书的, 所以就不行.

# 思考方案
那就思考一下有什么方法解决这个问题吧
1. 给本地服务器签发SSL证书
2. 让本地的服务走cloudflare的cdn, 只要cf到客户端这一段有https就行
那第一种方案显然是有点不太可行的, 麻烦就不说了, 而且还会直接暴露源服务器ip.

所以说就是第二种, 客户端->cf->源服务器了.
# 真正的Cloudflare优选
有关优选的一切Acofork后来又发了个[视频](https://www.bilibili.com/video/BV1QpSoBqERj/)来解释. 我这里不会过多阐述优选本身.

## 我之前的误区
我之前认为的cdn就是把源服务器的ip映射到cdn的那个ip上, 其实不然, 不管你是优选的还是直接开的小黄云, cdn这个东西其实是源服务器上的**服务**(80/443)映射到cdn的80/443(端口不重要, 只是阐述是**服务-服务**). 这也就是和A/AAAA记录的区别.

所以这也说明了我之前访问的其实是未经优选的域名, 因为是**ip+端口**的形式访问的.而优选其实是成功了的, 只不过我一直在访问非80/443端口, 这其实是非法访问.
## Lucky反向代理
那就监听80端口呗, 但是如果有多个服务扩展性就不好了, 所以说就用Lucky做一次反向代理.

到Lucky-Web服务, 添加一条规则, 监听端口改成80, 再添加一条子规则, 前端地址填最终访问的域名, 后端地址填本地的服务地址(形如http://127.0.0.1:8080).

这样CF回源到本地80端口的时候就会到Lucky, 根据Head里的最终访问域名, 反代到对应的服务.

所以整体就是: 客户端访问a.540881.xyz, 经过cf优选, 回源到源服务器, 源服务器根据Head里面的a.540881.xyz反向代理至对应服务, 服务响应请求, 返回至客户端.
## 本质
其实你有没有发现, Lucky用反代隐藏了你的端口, 而cf隐藏了你的ip, 所以其实CF是也可以算是你服务器的一个反向代理.

# 虚假的https
那现在终于处理好http的访问了, 那就是时候解决最初的问题了--https.

其实这个非常简单, 只要去Cloudflare的SSL/TLS把模式改成灵活即可:![](../assets/images/截屏2026-05-01%2021.05.47.png)
这样客户端-Cloudflare这段就是https用443端口, Cloudflare-源服务器还是http所以还是监听80端口, 所以不用动其他的东西了.

后续如果想改回Full也只需要在客户端装一个Cloudflare Origin证书或者直接申请一个, 不过也算是实现了"虚假的https"了.
# 我得到了什么?
这次可以说是解决了挺多问题的:
- 一定程度隐藏源站ip, 至少平时分发出去服务都是隐藏了的.
- https加密, 至少可以在一些强制https的场景伪装一下.
- 双栈访问(意外收获), 因为我只有ipv6公网, 而cloudflare会自动处理双栈, 所以v4和v6都可以访问服务器上的服务了.

但是也是有局限性的, 比如只有http/https可以用, 其他tcp/udp的服务还是得直接走服务器.

还有像流媒体这种高流量的场景也用不了, 不过也算可以了.

~~话说还有3天考AP我在这折腾这个真的可以吗?~~(注:此文章写于05/01, 而05/04要考微观经济)