---
title: 如何使用Manim制作数学动画
published: 2026-01-21
description: Manim从入门到精通:)
image: ""
tags: []
category: ""
draft: false
lang: ""
---
# 什么是Manim
如果看过3b1b的数学科普视频, 就可以发现他每一期的视频风格都是一样的, 其实就是3b1b自己写了个Python库来做动画, 这个库就是Manim, 可以通过写代码来制作动画.

# 下载
因为是Python库, 所以可以用pip来安装, 直接在终端执行:
`pip install manim`

# 构建参数
我们可以直接使用`manim -pql project.py Project` 来使用480p15的画质来构建渲染动画
## 一些常用选项
-  `-p` 以视频输出
- `-s` 输出最后一帧的图片
- `-ql` 以低画质(480p15)输出
- `-qh` 以高画质(1080p60)输出
- `--disable_caching` 不加这个manim会用之前的缓存文件而不是重新渲染所有以提升渲染速度, 但有的时候会出问题, 所以当你意识到不对的时候那就加上这个试试

渲染完之后可以在`/media` 下的对应目录下查看渲染文件

# 基本类
## Scene
Scene, 顾名思义, 就是你在Manim中要创建的动画所处的场景:
```python
from manim import *

class Project(Scene):
	def construct(self):
		// your code
```
这是Manim的程序入口, Project 类继承了Scene, 所以Project就是一个Scene, construct下就是我们构建生成视频会调用的方法, 传入的self就是Project类.

另外, 如果是3D场景, 需要使用`ThreeDScene`
### 常用方法
- `self.play(Animation)` 在Scene内播放Animation
- `self.add(Mobject)` 在Scene内添加Mobject, 可以添加多个Mobject
- `self.remove(Mobject)` 移除Scene中的Mobject, 这个是直接清除了, 后续无法操作对应的Mobject, 可以添加多个Mobject
- `self.wait(second)`  等待`second`秒, 默认1秒
- `self.set_camera_orientation(phi=, theta=)` 设置相机朝向, 其中phi和theta都是角度(要`*DEGREES`), phi角是平视坐标轴, 相机$(x,z)$或$(y,z)$和原点的夹角, theta角是俯视, 相机$(x,y)$和原点的夹角
- `self.move_camera(phi=, theta=)` 这个和`set_camera_orientation()`一样, 只不过是有动画的
- `self.add_fixed_in_frame_mobjects(mobject)` 这个通常是用来固定文字, 就是把Mobject固定在相机前

## Mobject
这个就是你在Scene里创建的物体, 图形, 函数, 坐标轴, 都是Mobject

### Mobject的通用属性
- color 颜色, [manim](https://docs.manim.community/en/stable/reference/manim.utils.color.manim_colors.html#module-manim.utils.color.manim_colors)提供了诸如RED, BLUE, GREEN一类的常数, 也可以使用[`ManimColor`](https://docs.manim.community/en/stable/reference/manim.utils.color.core.ManimColor.html)来创建颜色
- opacity 透明度, 0-1的一个float, 1为完全不透明, 0为完全透明
### 方位
#### 方向常量
- `UP`, `DOWN`, `LEFT`, `RIGHT` 上下左右
- `UL`, `UR`, `DL`, `DR` 左上, 右上, 左下, 右下
#### 相对位置移动方法
- `mobject.to_corner(direction)` 移动到角落
- `mobject.next_to(mobject, direction, buff=a)` 间距为a, 移动在mobject的direction方向
- `mobject.shift(direction)` 向direction平移
#### 绝对位置移动方法
- `mobject.move_to(x,y,z)` 移动到(x,y,z)的位置, 如果是坐标系里的点记得加c2p()
### 常见的Mobject
#### 坐标轴
其实坐标轴并不是Mobject, 它是一个有很多Mobject组成的Group
```python
Axes(
    x_range=[min,max,step],
    y_range=[min,max,step],# 坐标轴的数值范围, step是你坐标轴一格的距离
    x_length=len,
    y_length=len,# 坐标轴的在屏幕中实际显示的长度
).add_coordinates()#可以根据step标数字
```
`ThreeDAxes`是3D版的, 就再多个`z_range`和`z_length`
##### 方法
- `Axes.x_axis.set_opacity(opacity)` 设置透明度, 同理也可以调用其他Mobject的通用方法
- `Axes.plot(func)` 其中func是你的函数, 一般可以用lambda 来定义, 这个可以将你的函数转为在Axes上的图像(Mobject)
- `Axes.c2p(x,y)` 或`Axes.coords_to_point(x,y)` 可以将坐标系上的坐标转换为Scene的实际坐标
#### 函数
```python
f=lambda x:x**2
graph_f=axes.plot(f)
```
- 这里用lambda匿名函数, 实际上直接定义一个f(x)也可以
- return  的值不一定就是数学表达式, 可以用if, for, numpy之内的
- 函数也是可以`f(a)`来调用的, 后面会讲到
#### 图形
```python
Square(side_length=a)# 创建一个边长为a的正方形
Circle(radius=r)# 创建一个半径为r的圆
Prism(dimensions=[x,y,z])# 生成一个棱柱, dimensions里分别是相对于x,y,z轴的长度
```
这里要注意Prism的dimemsions显示出来是原来的一半, 我也不知道为啥
#### 公式
用这个之前要先确认一下电脑里面有没有LaTex环境, 可以在[这里](https://www.latex-project.org/)下载, 也可以在命令行用apt或homebrew之类的包管理下, LaTex的包特别大, 有4-5G, 下之前做好心理准备
```python
tex_f=MathTex(r"y=x^{2}")
```
就这样就可以生成一个Tex了, 注意字符串双引号前加r使用原始字符串

如果不知道LaTex怎么写, 可以用[Desmos](https://www.desmos.com/calculator)写好然后复制出来就是LaTex
#### 点, 线, 与面积
```python
dot=Dot(c2p(x,y,z))# 一个位于(x,y)或(x,y,z)的点
line=Line(c2p(x0,y0,z0),c2p(x1,y1,z1))# 由(x0,y0,z0)和(x1,y1,z1)连成的线
area=axes.get_area(graph_f,# 由f的图像和g的图像围成的面积
	bounded_graph=g,
	x_range=[l,r];# 在[l,r]的区间内的面积
	)
```

## Animation
顾名思义, 就是动画, 可以play()出来的那种
### 常见方法
- `Create(mobject)` 有点难形容, 从一个点扫一条线, 然后那条线扫过的地方出现
- `Write(mobject)` 常用于字, 就是一笔一笔写出来
- `FadeIn(mobject)` 渐入
- `FadeOut(mobject)` 渐出, 这个和`remove(mobject)`的区别是`remove(mobject)`是直接删, 和`mobject.set_opacity(0)`的区别是`mobject.set_opacity(0)`没有过程
- `Transform(mobject1, mobject2)` 这个很帅的, 从mobject1变形成mobject2, 但是这同时会把mobject2赋值到mobject1上, 要注意下

# valueTracker
就和Desmos 里的那个变量一样, 你可以设定值, 然后可以变, ValueTracker是一个小数
```python
lower_func=lambda x:x**2
upper_func=lambda x:math.sqrt(x)

x=ValueTracker(0)# 初始化一个ValueTracker, 值为0
length_line = always_redraw(lambda: Line(# 记得加always_redraw
            c2p(x.get_value(), lower_func(x.get_value()), 0),# 调用x的值
            c2p(x.get_value(), upper_func(x.get_value()), 0),
        ))# 垂直于x轴, upper_func和lower_func中间的一条线
# 播放一个将x设置为1, 持续2秒, 速度为线性的动画
self.play(Create(length_line))
self.play(x.animate.set_value(1), run_time=2, rate_func=linear)
```
- `always_redraw`可以让length_line不是只在对象创建时才渲染一次, 这样子每次x变化时也可以, 不过`always_redraw`后面跟的是一个function所以得加个lambda:
# Group
Group是由很多个Mobject组成的, 来实现对Mobject的批量操作
```python
>>> vg = VGroup()
>>> triangle, square = Triangle(), Square()
>>> vg.add(triangle)# 加了个triangle
VGroup(Triangle)
>>> vg += square# 加个个square
>>> vg  
VGroup(Triangle, Square)
>>> vg.remove(triangle)# 减了个triangle
VGroup(Square)
>>> vg -= square# 减了个square
>>> vg
VGroup()
```
- 这段代码直接复制的Manim文档里的,[在这](https://docs.manim.community/en/stable/reference/manim.mobject.types.vectorized_mobject.VGroup.html)
- 这个是VGroup, 还有个叫PGroup是操作点的, 没用过不知道
- 对Group操作就和对Mobject一样操作就行了
## Surface
其实可以不讲的, 但是我用了, 所以我要讲, 而且这个也很帅.

而且你猜我为啥放到Group下面--因为这玩意也是个Group.
```python
surface = Surface(
            lambda u, v: plane.c2p(# 这个我看不懂, GPT写的, 输入u,v返回一个点
                u, # 这个就是x轴坐标
                func(u) * np.cos(v),# 我理解的是(cos v,sin v)这是个圆
                func(u) * np.sin(v)# 所以是在yOz这个坐标系下的圆
            ),
            u_range=[0.01, 1],# 应该是[0,1]这个区间
            v_range=[0, TAU],# 应该是[0˚,360˚]这个区间
            resolution=(50, 100),# 分辨率, 调越大渲染越慢, 但是可以避免那种刺儿
            fill_opacity=0.6,
            checkerboard_colors=[GREEN, GREEN]
        )

```
- 这玩意的原理就是算一堆点, 然后连起来, 然后再把面也填个色, 大概
# 结语
- Manim这玩意还是很好玩的, 不过也确实没Blender这种做的快, 毕竟只是用来做数学动画的嘛:)
- 以上全部参考了[我的一个项目](https://github.com/re01redstone/AP-Calculus-BC-Manim-animation), 包括很多调bug时的经验, 让想玩玩Manim的少踩坑了属于是, 反正哪里遇到问题了欢迎看看这篇blog, 可能会对你有一些帮助, 当然也希望可以给我的项目提提issue&pr,顺便点个star:)