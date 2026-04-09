---
title: 我与科学上网的那些事儿
published: 2026-04-05
description: 我与GFW的爱恨情仇
image: ""
tags: []
category: ""
draft: false
lang: ""
---
>[!WARNING]
>本文仅为探讨VPN以及其他工具的理论知识, 不提供任何具体的"翻墙"工具.
>使用不正规的"翻墙"工具是危害国家网络安全的违法行为.

# 一切的起点
话要说到我小学的时候了, 那时候疫情上网课摸鱼, 经常就会去访问一些奇奇怪怪的网站(当然是正经的), 当时我就发现有一些网站没有办法访问, 或者需要极其长的时间才能访问, 这也是我第一次接触到"墙"的存在, 只不过当时还不知道这是"墙", 我以为单纯就是网卡了.

后来又了解到了GIthub, 我发现也进不去, 不过有的时候还是可以的, 所以我也就没咋当回事.

# 第一阶段 - 特定服务代理
后来又了解到了Steam这个游戏平台, 然后在看教程的时候就了解到了一个叫[Steam++](https://steampp.net/)(现Watt Toolkit)的东西. 大概就是和UU加速器那种差不多, 当然这种东西其实也不咋好用, 经常还是会很卡.

Github也是一样, 当时刷B站刷到一个Github加速的东西, 然后我就下了. 这个倒是挺好用的, 不过名字记不清了. 在有了那个工具之前, 我进Github都还是纯看脸, 就一直在那刷新看哪次能刷进去:(

像这类工具的原理其实也并没有涉及到代理或VPN等手段, 只是把特定域名解析出来的ip测速, 然后去访问速度最快的那个ip.
# 第二阶段 - VPN
后来也不知道啥时候就知道VPN这个东西了, 反正当时就自己找了一个用. 叫QuickQ, 不过现在不知道还有没有了. 用了大概有一年, 又换成了另一个叫Veee的.

不过确实, VPN可比特定服务的代理强太多了.

另外, 顺便再解释一下VPN是什么, Virtual Private Network, 虚拟专用网络, 其实这个用途还是很广泛的, 如果你想访问另一个人的主机, 但是你们又不处于同一个局域网, 这时候VPN就相当于一个隧道, 把你们拉到一个虚拟的网络里面, 外面的人无法访问, 因为你们的数据在隧道内被加密了.

当然, VPN也可以被用于远程联机, 和"翻墙"了, 因为数据是加密过的嘛.

你通过VPN连接到远程的代理服务器, 代理服务器再替你访问你想访问的内容, 就是这么简单. 不过这种VPN的协议和普通的协议不同, 如果"飞机"飞的不高, 就很容易被打下来. 现在的墙不是看内容, 而是直接看流量特征了.

如果想了解更多可以看[这个](https://www.bilibili.com/video/BV16QAuzJELC).

# 第三阶段 - GFW
再之后, 就开始好奇这一切背后的原理了, 在墙外搜这些东西还是搜的到的.

*那么是何方神圣遮住了墙内的我们想要往外看的眼睛呢?* 这个东西, 当然就是大名鼎鼎的Great Firewall(GFW), 也就是俗称的"墙".

经过我这些年零零散散对墙的理解, 大概还是总结出了这几种墙的原理.
## DNS污染
这也是最常见的一种, 通过伪造证书(参见[CNNIC](https://en.wikipedia.org/wiki/China_Internet_Network_Information_Center#Certificate_issuance_violations))或注入ISP, 让你获得到错误的DNS解析, 不过这个东西只影响域名解析, 你如果直接访问ip的话就没用了. 

这个东西还搞出来了非常严重的隐私泄漏问题, 参见[WallBleed](https://gfw.report/publications/ndss25/en/)
## RST阻断
这个是网络层的东西了, TCP在发数据包的时候, 如果收到了RST包, 就会中断发送, 然后返回`connection was reset`之类的.

这个也是GFW最主力的手段了.

我暂时是就知道这些了, 更多可以参见[某个人的博客](https://blog.yvling.cn/posts/%E5%8D%9A%E5%AE%A2%E6%96%87%E6%A1%A3/%E6%8A%80%E6%9C%AF%E7%A0%94%E7%A9%B6/2025-02-04-%E5%85%B3%E4%BA%8E-gfw-%E7%9A%84%E4%B8%80%E4%BA%9B%E6%80%9D%E8%80%83/)

# 第四阶段 - 机场
这就是我近期了解到的了, 还记得我刚才讲的飞机与防空炮的例子吗, 机场就是飞机降落的地方, 也就是代理服务器, 与VPN的区别就在于它的飞机采用了更先进的协议, 飞的更高, 不容易被打下来, 被流量特征识别. 不过这些机场大多都是个人或者~~团伙~~组织, 价格会比VPN要便宜, 灵活度也更高. 不过数据的安全性就不怎么保证了.

这也是相当的现代翻墙技术了, 有着非常强大的开源社区.

这些工具一般都会涉及到一些专有名词:
- 客户端 - Clash等, 集成的图形化界面.
- 内核 - Xray等, 提供最核心的流量加密传输.
- 协议 - Hysteria等, 通信的"语言", 通过一系列手段防止被GFW识别.
- 机场 - "飞机降落的地方", 代理服务器, 提供最终流量的访问.

更多有关机场也可以参考[二猫子的博客](https://www.ermao.net/posts/vpn/)

不过机场是真的便宜啊.
## 命令行部署
搞这个的时候还踩了挺多坑的, 讲一下.

但是其实后来又发现了个叫[subconverter](https://github.com/tindy2013/subconverter)的, 可以直接curl然后生成`config.yaml`

......
### Mihomo
Clash一般来讲都是图像化界面, 所以命令行就比较难搞了. Clash的内核是[Mihomo](https://wiki.metacubex.one/startup)所以要先在下一个Mihomo内核. 

我是直接下的`.deb`包, 就直接`apt install ./file.deb`就行, 下完之后添加一下[systemd服务](https://wiki.metacubex.one/startup/service/), 如果是用包管理器下的就不用挪文件到`/usr/local/bin`和配置到`/etc/mihomo`了.

之后配置文件就在`/etc/mihomo/config.yaml`, 这个配置文件不是完整版的那种, 完整版的[在这](https://github.com/MetaCubeX/mihomo/blob/Meta/docs/config.yaml). 所以说你要啥写啥就行了.
### Mihomosh
但是你之后导入订阅链接肯定就不能你自己每次都开一遍配置文件了, 可以用[mihomosh](https://github.com/SamuNatsu/mihomosh), release里面下完直接是二进制文件.

下完之后可能会说权限不够, 你当然可以sudo, 不过也可以`chmod 755 ./mihomosh`. 我刚学的, 改文件访问权限:)

但是现在你还用不了, 得先去`/etc/mihomo/config.yaml`里添加一下外置api, 加一行`external-controller: 0.0.0.0:9090`, 如果只想本地访问可以输`127.0.0.1:9090`. 如果想加个秘钥也可以再加一行`secret: "yourPassword"`

之后就可以用mihonosh了, 不过每次mihonosh更新配置文件的时候都会把原来的覆盖, 所以还需要添加全局扩展配置(Global Extend Config), 在安装目录下`./mihomosh profile egxc`, `-e <editor>`可以用指定编辑器打开, 同样加上`external-controller`和`secret`的配置就行. 它会在添加完所以配置之后再加上这些配置.

现在就可以添加配置文件了, `./mihomosh profile create <name>`, 创建一个profile, 改成下面的样子:
```yaml
## Mihomosh profile info

# Profile name (REQUIRED)
# 名字, 随便填一个
name: aaa

# Profile type (REQUIRED; One of `remote` or `local`)
type: remote

# Profile URL (Required when type is `remote`)
# 填你的订阅链接
url: your_subscription_url

# User agent for updating (Required when type is `remote`)
user-agent: mihomosh/v2.2.0 (clash-verge)

# Proxy for updating (Required when type is `remote`; One of `none`, `system` or `mihomo`)
proxy: none

# Whether to allow the invalid certificates (Required when type is `remote`)
allow-invalid-certs: false

```
最后输入`./mihomosh activate <name>`激活订阅文件就行.

如果要更新就`./mihomosh update <name>`, 显然这个似乎并不能自动更新(因为它没让你加systemd), 不过这样你也不用手动改配置文件了不是吗.

最后再记录一下我踩过的一个坑, 如果要添加规则, 需要用全局扩展脚本(Global Extend Script), 输入`./mihomosh profile egxs` 然后改成这样:
```javascript
function main(config) {
  // 你的规则集
  const prependRules = [
    "DST-PORT,22,DIRECT",
  ];
  config.rules = [...prependRules, ...config.rules];
  return config;
}
```
要不然如果用全局扩展配置的话会覆盖掉之前的config.
### webUI
就是可以在网页版的UI界面. 这个就我用的[metacubexd](https://github.com/MetaCubeX/metacubexd), 有三种方式:
- 在[Github Pages](https://github.com/MetaCubeX/metacubexd/deployments/github-pages)
- 直接`git clone https://github.com/metacubex/metacubexd.git -b gh-pages /etc/mihomo/ui`(如果装了git且能访问Github).
- [线上面板]([https://metacubexd.pages.dev](https://metacubexd.pages.dev/)), Endpoint可以填内网地址.
第一种要下完之后要放到`/etc/mihomo`路径下, 是一个文件夹放到这里嗷.

然后在config.yaml里加一行`external-ui: <folder>`, 就填你刚才新放进去的那个文件夹的名字, 这一步三种方法都要搞. 如果用了Mihomosh也得在全局扩展配置里加这一行.

最后在浏览器访问`ip:9090/ui`就可以登录终端了.
# 结语
总之, 这就是我科学上网的那些事了, 也希望能帮到看到这篇博客的, 还没有科学上网能力的人, 了解一下都有哪些方法, 少走弯路. 希望可以方便到你们.

最后, *想看世界的眼睛是遮不住的*. GFW终究是会被一个一个人所翻过去, 所以真的没必要, 互联网应当是自由的.