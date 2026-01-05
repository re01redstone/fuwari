---
title: 如何在Terminal中使用代理
published: 2026-01-05
description: 众所周知,在终端上如果要下载东西,用镜像站会特别慢,更何况如curl一类的工具无法使用镜像站
image: ""
tags: []
category: ""
draft: false
lang: ""
---
### 临时使用
这个是对当前会话生效,就是说如果重启或者关闭了终端窗口(如果有的话)就会重置
首先,你需要查看你VPN或代理的端口,比如mac就在设置->网络->Wi-Fi->详细信息->代理,界面长这个样子:![](../assets/images/截屏2026-01-05%2013.08.24.png)
如果你的界面不长这个样子,那检查下你代理开没开
然后,你找到HTTP和HTTPS的代理的端口,记下来(正常来说这两个端口都是一样的,所以只用记一个),注意别记SOCKS代理的端口!
然后,在你的终端里输入下面这两串神秘小代码
```bash
export http_proxy=http://127.0.0.1:你刚才的端口
export https_proxy=$http_proxy
```
如果想取消也很简单
```bash
unset http_proxy https_proxy
```
### 永久使用
其实是没有直接的配置的,所以我们需要写一个脚本,但是如果要开机自启又需要systemd之类的服务,太过于麻烦了,所以就直接写一个脚本每次要开代理的时候运行一下
比如:
```bash
function proxy_on() {
    export http_proxy=http://127.0.0.1:你记的端口
    export https_proxy=$http_proxy
    echo -e "终端代理已开启"
}

function proxy_off(){
    unset http_proxy https_proxy
    echo -e "终端代理已关闭"
}

```
我们把这些代码放到``~/.zprofile``或``~/.bashprofile``里,这样只需要每次开机的时候执行一次``proxy_on``就可以打开代理,执行一次``proxy_off``就可以关闭代理了(如果没效果可以先执行一次``source ~/.zprofile``或``source ~/.bashprofile``)
### 原理
具体来说,``http_proxy`` 和``https_proxy`` 默认是空的,如果里面有值就会走代理,代理实现代理的功能就是让所有服务的流量走VPN应用的端口,然后走VPN的中继服务器,VPN软件默认只设置了图形化界面的代理端口,所以才需要在终端再设置一遍终端的代理端口,让终端也走代理的流量