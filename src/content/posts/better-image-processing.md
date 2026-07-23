---
title: 更优雅便携的图片格式处理
published: 2026-07-23
description: 不想用PS和在线图片编辑又能怎么办呢
image: ""
tags: []
category: ""
draft: false
lang: ""
---
# 背景
最近(其实已经是一个多月前的事了)我要给我的网站放一个Q群二维码, 但是众所周知, Q群的那个二维码不是黑的就是白的, 而我的网站是有亮暗色模式的, 如果背景不是透明的话就得搞两张图放到仓库里, 非常不优雅.

所以就想办法把这张图扣成黑白的, 但是想必用过的各位都知道, 现在除了PS这种专业图片处理软件都非常的一言难尽, 动不动就是会员登录加水印, 免费的用起来也是一言难尽啊.

不过对于这种纯色背景, 根本不需要那么高大上的算法去抠图. 直接用图片处理软件的老祖--**imagemagick**就可以了.

其实之前还试过用python写图片处理脚本, 不过没有必要重复造轮子嘛.

# 这是啥

>[!CITE]
>ImageMagick[®](http://tarr.uspto.gov/servlet/tarr?regser=serial&entry=78333969) is a free, [open-source](https://imagemagick.org/license/) software suite, used for editing and manipulating digital images. It can be used to create, edit, compose, or convert bitmap images, and supports a wide range of file [formats](https://imagemagick.org/formats/), including JPEG, PNG, GIF, TIFF, and Ultra HDR.

总而言之, 这是一个开源的, 图片处理的底层库, 和ffmpeg是一类东西. 可以实现图片的格式转换、缩放、裁剪、滤镜、合成. 不过唯一的缺点是这是CLI软件, 所以你只能用terminal了(部分原因应该是用功能太多太杂GUI体验感反而不好)

# 怎么用
## 下载
直接在[官网](https://imagemagick.org/download/)下对应系统的版本就行, mac如果没下homebrew就去[下一个](https://brew.sh/).
## 使用
然后打开对应的terminal(Powershelll/cmd等).

先来解决下我的那个问题吧, 怎么把这个黑色背景扣掉:
```bash
magick ./raw.png -fuzz 60% -transparent black output.png
```
- `magick` 调用imageMagick, 如果说提示你没有这个命令, 那就是没加到PATH里, 输可执行文件的绝对路径
- `./raw.png`源文件就是当前目录下(你开terminal的那个地方)的raw.png
- `-fuzz 60%` 允许颜色容错60%, 毕竟也不一定所有的黑都是同一个RGB值
- `-transparent black` 把黑色(#000000)变成透明(Alpha=0)
- `output.png` 输出到当前目录下的output.png
怎么样, 很简单吧.

## 其他用法
其实[官方文档](https://imagemagick.org/command-line-processing/)讲的很详细了, 不过我挑几个场景讲讲, 反正也可以问AI嘛.
### 格式转换
- `magick input.jpg output.png` 后缀名改下就行
- `magick input.png -quality 85 output.jpg` 控制jpg压缩质量
- `magick *.jpg output.pdf`图片合并pdf(是的, 不用word转pdf了)
### 缩放
- `magick input.png -resize 50% output.png` 等比缩放到 50%
- `magick input.png -resize 800x600 output.png` 锁定纵横比, 缩放到指定尺寸
- `magick input.png -resize 800x600! output.png` 强制缩放到指定尺寸
### 去背景
- `magick input.png -fuzz 60% -transparent black output.png` 指定容错把指定颜色变成透明
### 旋转翻转
- `magick input.png -rotate 90 output.png` 顺时针转90度
- `magick input.png -flip output.png`垂直翻转
- `magick input.png -flop output.png`水平翻转
### 效果
- `magick input.png -blur 0x8 output.png` 高斯模糊, 半径x标准差是卷积核参数(半径一般填0, 标准差越大越猛就对了)
- `magick input.png -sharpen 0x3 output.png` 锐化, 0x3解释同上
- `magick input.png -negate output.png` 反色
- `magick input.png -fill red -colorize 30% output.png` 叠30%红色
- `magick input.png -modulate 80,120 output.png` 亮度80%, 饱和度120%
### 拼接
- `magick img1.png img2.png +append output.png` 横向拼接
- `magick img1.png img2.png -append output.png` 纵向拼接
### 隐写术解密
- `magick input.png -level 0%,30% output.png` 光棱坦克黑底显形, 调整色阶显示范围0-30
- `magick input.png -level 70%,100% output.png` 光棱坦克白底显形, 调整色阶显示范围70-100