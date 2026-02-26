---
title: OpenList, qBittorrent&PeerBanHelper的部署
published: 2026-02-24
description: 最近部署的一些小项目
image: ""
tags: []
category: ""
draft: false
lang: ""
---
## 前言
起因就是了解到BT下载这个东西, 然后觉得挺方便的, 然后正好有一台服务器, 而且还有闲置资源, 就想搭一个玩玩.

正好服务器也缺一个文件管理系统, 就顺便把Openlist也部署了, 其实也有一部分原因是因为想把电脑手机里的一部分文件迁移走.

## OpenList
OpenList就是Alist被卖之后继承Alist的"社区版", 其实一开始我还不知道Alist被卖了, 还好最后查文档的时候看到相关视频了, 要不然就数据裸奔了. ~~数据在哪~~ 

可能还有很多人不知道Alist是啥, Alist就是一个开源的网盘系统, 你可以挂载百度啊迅雷啊夸克啊123啊上面的网盘, 最主要我用的还是本地(Local)的网盘, 就是家里云. 你可以很方便的通过Alist网页版下载上传资源. 但是后面Alist原作者把项目卖掉了, 所以现在就用OpenList吧, 功能是几乎完全一样的. 另外说下, Alist本来也是原作者贡献代码最多的, 所以原作者也没有说是背叛了开源社区这回事. 反正我也是听说:)

### 下载&安装
总之, 我们现在可以用[OpenList](https://doc.oplist.org/), 当然, 如果你是Linux而且装curl了, 也有一键的[安装脚本](https://doc.oplist.org/guide/installation/script). 或者我给你复制一遍:
```bash
curl -fsSL https://res.oplist.org/script/v4.sh > install-openlist-v4.sh && sudo bash install-openlist-v4.sh
```
文档里面讲的很清楚, 安装脚本里也都是中文字儿, 我相信你搞的定的.

### 配置账号
安装好之后, 会给你初始的用户名和密码, 然后OpenList会在5244端口开个服务, 你直接浏览器访问, 如果没反应你就`systemctl restart openlist`重启一下OpenList的服务, 总之, 按照它给你的账号密码登进去, 往下面瞅一眼有一个manage/管理, 看到右边, 赶紧先把密码改了(账号名也可以改, 您随意) 你也可以加SSH和2FA.

在User/用户里可以设置不同用户的权限和添加删除用户(前提是你现在登的账号是管理员, 就是安装的时候给你的那个账号)
### 挂载文件
依旧管理页面, 到Storage/存储, 然后你要挂载本地文件夹就选Local/本机存储, 这里要注意Mount Path/挂载路径是显示在云盘里的路径, Root Folder Path/根文件夹路径是你机器里实际的路径, 总之不要搞混了, 剩下的都不用管, 直接Add/添加就行. 如果你要顺便挂载网盘的话也是一样, 一般就是填个账号密码. 更多[参考文档](https://doc.oplist.org/guide/drivers/common)

## qBittorrent
qBittorrent就是BT下载器啦, BT下载就是加了P2P技术的下载, 可以让你的下载速度不用取决于上传者的速度, 所以可以实现非常高速的下载, 可以看[这个视频](https://www.bilibili.com/video/BV1Pev8BLE7e)了解一下. 

### 下载&安装
我由于是纯命令行界面, 所以我就下`qbittorrent-nox`, 就是无GUI版本, 但是是有Web UI的, 如果是有GUI的系统还是在[官网](https://www.qbittorrent.org/download)下载有GUI的版本.

对于包管理是apt的发行版(其他的我也不知道):
```bash
add-apt-repository ppa:qbittorrent-team/qbittorrent-stable //添加PPA
apt install qbittorrent-nox //下载
```
然后你就下载了`qbittorrent-nox`,可以用`qbittorrent-nox --version`查看版本, 也可以`qbittorrent-nox`直接运行, 你就可以在主机的8080端口登录Web UI了, 默认的账号名是admin, 密码是adminadmin
### 注册systemd
现在下载嘛确实下载好了, 不过现在的qBittorrent还是在前台运行的, 你会话一关就没了, 所以要注册systemd服务, 这玩意就不用你自己费劲写了, 已经有前人写好的了, 当然也感谢[这位大佬](https://blog.sixiaolong.win/2025/10/06/%E9%80%82%E7%94%A8%E4%BA%8E-qbittorrent-%E7%9A%84-systemd/)的博客, 里面写的挺清楚的. 不过还是要注意几点的:
- `<User>`可以直接填root也可以自己创建个账号专门运行qBittorrent
- ExecStart那一行的`</path/to/qbittorrent-nox>`要填路径, 别直接填`qbittorrent-nox`, 反正我的默认安装路径是`/usr/bin/qbittorrent-nox`, 就填这个试试, 应该都是一样的
- 记得把注释删了
总之按照大佬的博客配置完之后就可以在主机的10180端口(在ExerStart那一行的`--webui-port=10180`)登录Web UI了, 依旧默认账号admin, 密码adminadmin
### 配置
首先往左上角瞅, 到Tools-Options点开, 找到Web UI那一栏, 大概是长这个样子:![](../assets/images/截屏2026-02-26%2017.37.42.png)
看到那个Languages了嘛, 改成你认识的, 我就不改了, 然后看到下面的Authentication再到底下的Password, 快点改了. 最后不要点直接点上面的叉, 划到最下面有个Save才能保存.
#### 保存
![](../assets/images/截屏2026-02-26%2018.48.38.png)
再看到Downloads这一栏Default Save Path就是默认保存路径, 下面Automatically add torrents from-Monitored Folder可以检测对应路径下的种子然后自动下载, 保存到下面这个Override Save Location里, 如果没有加这个Override Save Location的话那就是到Default Save Path里, 还有默认种下完之后会给你种子文件换位置了, 如果你想再保留一份种子文件就勾上Default Save Path下面那三项
#### 防PCDN检测
众所周知, 之前运营商由于Personal CDN的出现还有原本省间结算的机制, 导致高上传和省间流量还有高并发这样的流量特征变得比较容易出问题, 刚好BT下载就是就符合这些特征, 所以说还是要伪装一下滴.

不过各地的运营商打击PCDN的程度也不一样, 反正也适当搞一下.
![](../assets/images/截屏2026-02-26%2020.32.11.png)
到communication-Connections Limits 把第一个和第二个适当调第一点, 直接调成我这样也行
![](../assets/images/截屏2026-02-26%2020.34.08.png)
再到Speed里, Global Rate Limits 和Alternative Rate Limit的Upload/上传都设置一下限制,然后把Schedule the use of alternative rate limits 打开, 随便你选个时间段, 这样可以保证你流量特征不像PCDN.
![](../assets/images/截屏2026-02-26%2020.37.59.png)
再到BitTorrent-Seeding Limits, 把When ratio reaches打开, 随便设置个啥, 这样子你分享率够了就不会一直传.

## PeerBanHelper
防完了PCDN, 当然也得防一下迅雷这种只下载不上传的吸血客户端, 我们就需要PeerBanHelper了, 一个通过检测流量特征, 通过P2P网络投票等决定ban的节点的一个东西.
### 下载&安装
[文档在这](https://docs.pbh-btn.com/docs/setup/Linux/Deb), 顺便说下其实我还修了个typo:)

一般来讲按文档是可以直接安好的, 但可能你的系统没有Java 25环境, 所以你得自己下一个, 一般就`apt install openjdk-25-jre-headless`不过如果这个没法下的话是因为你的apt没有这么高版本的Java, 那你就得去下[Eclipse Temurin](https://adoptium.net/zh-CN/installation/linux)的Java了. 然后下完之后你可能还要去`/etc/systemd/system/peerbanhelper.service`把`ExecStart`那行的`java`改成`/usr/lib/jvm/temurin-25-jdk-amd64/bin/java`
### 配置
然后进到你主机的9898端口, 它会让你搞一些初始设置, 一般就直接按默认的来, 然后它叫你填啥就填啥
![](../assets/images/截屏2026-02-26%2021.14.52.png)
就是这个东西, 你的Endpoint是填这个, 然后你填完之后要让它check一下, 其他没了.

之后你就可以看到它在给你封一堆Ip了:)
## 结语
总之, 现在你可以用你的qBittorrent下片Openlist看片, 实现非常丝滑, 非常高清还免费的看片体验了, 另外, 如果你要看番, 我知道的是有[nyaa.si](https://nyaa.si)这个种子站.