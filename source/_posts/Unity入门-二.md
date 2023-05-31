---
title: Unity入门(二)
date: 2023-05-26 15:27:31
tags: Unity
categories: 
- 游戏开发
- 客户端
---

## Unity新手入门（二）

资料来源[擅码网-Monkey的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/34412870)

### UI系统

指UGUI

由UI游戏对象和UI组件

#### 两大UI游戏对象

**Canvas 画布**

- 画布Gameobject，是所有 UI Gameobject (如Image、text等)的父物体；

**EventSystem 事件系统**

- 用于响应和处理用户对UI的各种操作
- 同一场景内，Canvas可以有多个，EventSystem只能有一个

#### 组件

由两部分组成：

- UI布局组件 Layout
- UI元素组件 UI

<img src="Unity入门-二/image-20230526155807850.png" alt="image-20230526155807850" style="zoom: 67%;" />



##### Text组件

<img src="Unity入门-二/image-20230526160528295.png" alt="image-20230526160528295" style="zoom:67%;" />

字体.ttf格式

##### Text TMP组件

<img src="Unity入门-二/image-20230526160937644.png" alt="image-20230526160937644" style="zoom:67%;" />

字体.SDF格式

<img src="Unity入门-二/image-20230526162202757.png" alt="image-20230526162202757" style="zoom:80%;" />

<img src="Unity入门-二/image-20230526162352685.png" alt="image-20230526162352685" style="zoom:80%;" />

##### Image组件

常用的图片格式为PNG，可以存储透明通道。

- 图片素材的管理
  - <img src="Unity入门-二/image-20230526161620578.png" alt="image-20230526161620578" style="zoom:67%;" />
- 参数
  - <img src="Unity入门-二/image-20230526161749972.png" alt="image-20230526161749972" style="zoom:67%;" />



##### Button组件

**普通Button**

Interactable可交互

Transition过渡模式

- Color Tint颜色过渡
  - <img src="Unity入门-二/image-20230526163008977.png" alt="image-20230526163008977" style="zoom:80%;" />

**TMP Button**

- 跟普通button的区别在于子text物体的格式：普通和TMP两种格式

**点击事件**

- 点击按钮，触发事件，执行事件处理函数
- <img src="Unity入门-二/image-20230526163512810.png" alt="image-20230526163512810" style="zoom:80%;" />



#### 补充

<img src="Unity入门-二/image-20230526164636319.png" alt="image-20230526164636319" style="zoom:80%;" />

##### TextMeshPro插件

<img src="Unity入门-二/image-20230526164203169.png" alt="image-20230526164203169" style="zoom:80%;" />

![image-20230526164441402](Unity入门-二/image-20230526164441402.png)



TMP中文字体文件的实质是**图集**





### 特效系统

Unity的特效系统主要是

- 粒子系统 Particle System
- 力场 Force Field
- 拖尾 Trail
- 线 Line

#### 拖尾渲染器组件

**参数**

- 宽度设置
- 其他
  - <img src="Unity入门-二/image-20230526170224474.png" alt="image-20230526170224474" style="zoom:80%;" />

**材质**

<img src="Unity入门-二/image-20230526170434673.png" alt="image-20230526170434673" style="zoom:80%;" />

**调试面板**

![image-20230526170622052](Unity入门-二/image-20230526170622052.png)

**细节**

<img src="Unity入门-二/image-20230526170836210.png" alt="image-20230526170836210" style="zoom:80%;" />





#### 线渲染器

用途：绘制“特效线”，如激光，台球线，或各种辅助线。



基本使用

<img src="Unity入门-二/image-20230527190643282.png" alt="image-20230527190643282" style="zoom:80%;" />

<img src="Unity入门-二/image-20230527191836723.png" alt="image-20230527191836723" style="zoom:80%;" />



### 音效系统

各种音效

<img src="Unity入门-二/image-20230527191958959.png" alt="image-20230527191958959" style="zoom:80%;" />



Unity内的音效系统，两大核心：

- 播放音效
- 监听音效



音效产生器：

- AudioSource
  - 参数：<img src="Unity入门-二/image-20230527193709932.png" alt="image-20230527193709932" style="zoom:80%;" />
  - <img src="Unity入门-二/image-20230527193727920.png" alt="image-20230527193727920" style="zoom:80%;" />

音效文件：

- AudioClip

音频监听器：

- AudioListener



### 常用API

---

#### Resource类

- 用于资源文件的加载
- 需要先创建/Assets/Resources文件夹存放资源文件
- 常用资源类型
  - <img src="Unity入门-二/image-20230527194338981.png" alt="image-20230527194338981" style="zoom:80%;" />



**单个资源加载**

![image-20230527194551425](Unity入门-二/image-20230527194551425.png)



**多个资源加载**

![image-20230527194745834](Unity入门-二/image-20230527194745834.png)





#### AudioSource

**AudioSource组件**

AudioSource类的.clip字段对应AudioSource组件的AudioClip参数



AudioSource组件的使用

![image-20230527195339288](Unity入门-二/image-20230527195339288.png)



**AudioSource类**

- 在指定位置播放小段音频（one shot）
  - <img src="Unity入门-二/image-20230527195604149.png" alt="image-20230527195604149" style="zoom:80%;" />
  - <img src="Unity入门-二/image-20230527195634797.png" alt="image-20230527195634797" style="zoom:80%;" />



#### 游戏物体设置

**两类资源**

<img src="Unity入门-二/image-20230527200104757.png" alt="image-20230527200104757" style="zoom:80%;" />

- 纯资源
- GameObject资源
  - 需要多一步实例化instantiate，相当于new出来



##### 实例化游戏物体

**API**

![image-20230527200451065](Unity入门-二/image-20230527200451065.png)



**预制体**

![image-20230527200739291](Unity入门-二/image-20230527200739291.png)



**实例化操作的作用**

<img src="Unity入门-二/image-20230527200913883.png" alt="image-20230527200913883" style="zoom:80%;" />





##### 销毁游戏物体

- 实例化和销毁对游戏物体的出生和死亡
- 比在如战死后进行销毁



**API**

<img src="Unity入门-二/image-20230527201131059.png" alt="image-20230527201131059" style="zoom:80%;" />



##### 添加/移除组件

<img src="Unity入门-二/image-20230527201356611.png" alt="image-20230527201356611" style="zoom:80%;" />



<img src="Unity入门-二/image-20230527201424529.png" alt="image-20230527201424529" style="zoom:80%;" />



移除方式：

- 使用GetComponent\<T>()获取组件
- 使用Destroy()销毁组件



#### 脚本生命周期

**三种状态**

<img src="Unity入门-二/image-20230527201712437.png" alt="image-20230527201712437" style="zoom:80%;" />



**脚本生命周期**

**GameObject生命周期**

![img](Unity入门-二/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxNDgwODM5,size_16,color_FFFFFF,t_70.png)



**脚本生命周期**（MonoBehaviour类的子类）

**说明**

开启状态：

![image-20230527202238558](Unity入门-二/image-20230527202238558.png)

- awake和start只会执行一次



进行中状态：

![image-20230527202645563](Unity入门-二/image-20230527202645563.png)



结束状态：

![image-20230527202909950](Unity入门-二/image-20230527202909950.png)





**其他问题**

Awake和Start

![image-20230527203357409](Unity入门-二/image-20230527203357409.png)

- A脚本调用B脚本为例，B脚本的初始化操作要放在Awake内完成，然后A脚本在Start内查找B脚本并调用其方法。







#### Invoke函数

延迟调用、重复调用

![image-20230527205946049](Unity入门-二/image-20230527205946049.png)

**API**

<img src="Unity入门-二/image-20230527210036874.png" alt="image-20230527210036874" style="zoom:80%;" />

<img src="Unity入门-二/image-20230527210217620.png" alt="image-20230527210217620" style="zoom:80%;" />

<img src="Unity入门-二/image-20230527210254671.png" alt="image-20230527210254671" style="zoom:80%;" />





#### 协程

**介绍**

<img src="Unity入门-二/image-20230527210709676.png" alt="image-20230527210709676" style="zoom:80%;" />

- 协程方法
- 协程操作：开启，关闭

<img src="Unity入门-二/image-20230527210756780.png" alt="image-20230527210756780" style="zoom:80%;" />

- Unity的线程比较特殊，大多数内容在主线程中执行。
- 子线程无法调用引擎相关API，于是采用协程来替代线程。



**语法**

- 协程方法的编写
  - <img src="Unity入门-二/image-20230527211004688.png" alt="image-20230527211004688" style="zoom:80%;" />
- 开启协程
  - <img src="Unity入门-二/image-20230527211644562.png" alt="image-20230527211644562" style="zoom:80%;" />
- 终止协程
  - <img src="Unity入门-二/image-20230527211809047.png" alt="image-20230527211809047" style="zoom:80%;" />



**应用**

- 物体移动，每帧移动一点点
  - while循环配合yield return null
  - <img src="Unity入门-二/image-20230527213237458.png" alt="image-20230527213237458" style="zoom:80%;" />
- 。
- 。
- 。



**生命周期**

![image-20230527213458805](Unity入门-二/image-20230527213458805.png)

- 不使用协程，while循环的代码会在1帧内完成。
- 使用协程，可以达到每帧分步完成的效果

![image-20230527213807780](Unity入门-二/image-20230527213807780.png)



**协程的运行时机**

- ![image-20230527213329201](Unity入门-二/image-20230527213329201.png)
- ![image-20230527213339744](Unity入门-二/image-20230527213339744.png)



**由其他脚本调用的情况**

- 原始：其他脚本使用该脚本的引用来调用StartCorotine
- 常用：该脚本封装好协程开启和关闭的代码，供其他脚本调用

![image-20230527214016638](Unity入门-二/image-20230527214016638.png)



**协程与Invoke对比**

- 相同之处
  - 都是MonoBehaviour类中定义的公开方法
  - 都可以实现延迟调用
- 不同之处
  - 协程可以动态传递参数，Invoke只能使用无参方法
  - 协程的方法体内可以多次延迟，Invoke只能在开启时延迟



#### 消息发送

问题：

对于同一个游戏物体的脚本组件 ——

 <img src="Unity入门-二/image-20230529151745528.png" alt="image-20230529151745528" style="zoom: 67%;" />

解决：

- 给同级、子级脚本发送消息

<img src="Unity入门-二/image-20230529151838922.png" alt="image-20230529151838922" style="zoom:67%;" />

- 给同级、子级脚本广播发送消息

<img src="Unity入门-二/image-20230529152349402.png" alt="image-20230529152349402" style="zoom: 67%;" />

- 给父级脚本发送消息

<img src="Unity入门-二/image-20230529161205066.png" alt="image-20230529161205066" style="zoom:67%;" />





#### GameObject查找

**全局名称查找**

-  <img src="Unity入门-二/image-20230529162445884.png" alt="image-20230529162445884" style="zoom:50%;" />
-  <img src="Unity入门-二/image-20230529162601624.png" alt="image-20230529162601624" style="zoom:50%;" /> 
   - 如果要持有隐藏的物体：<img src="Unity入门-二/image-20230529163003658.png" alt="image-20230529163003658" style="zoom:50%;" />
-  含有重名的游戏物体的情况：
   -  <img src="Unity入门-二/image-20230529163142987.png" alt="image-20230529163142987" style="zoom:50%;" />

采用路径查找

-  <img src="Unity入门-二/image-20230529163233301.png" alt="image-20230529163233301" style="zoom: 50%;" />





**全局标签查找**

 <img src="Unity入门-二/image-20230529163616238.png" alt="image-20230529163616238" style="zoom:50%;" />

**API**

<img src="Unity入门-二/image-20230529163710271.png" alt="image-20230529163710271" style="zoom:67%;" />







#### Transform查找

**查找子物体**

 <img src="Unity入门-二/image-20230529165243944.png" alt="image-20230529165243944" style="zoom: 50%;" />

 <img src="Unity入门-二/image-20230529165407539.png" alt="image-20230529165407539" style="zoom:50%;" />



**查找子物体组件**

 <img src="Unity入门-二/image-20230529165518177.png" alt="image-20230529165518177" style="zoom:50%;" />

可以获取多层嵌套的子物体及其组件

 <img src="Unity入门-二/image-20230529165757497.png" alt="image-20230529165757497" style="zoom:50%;" />





**两种查找的对比**

<img src="Unity入门-二/image-20230529211353535.png" alt="image-20230529211353535" style="zoom:80%;" />



### 物理射线

- 可以与collider发生碰撞
- 从**起点**往**指定方向**无限延申的物理射线



#### 创建物理射线

- 射线结构体：**Ray**
- 创建方式：
  - 直接new一个Ray对象
  - 通过摄像机相关API构造
    -  <img src="Unity入门-二/image-20230531153113688.png" alt="image-20230531153113688" style="zoom:67%;" />

**主摄像机**

 <img src="Unity入门-二/image-20230531152955214.png" alt="image-20230531152955214" style="zoom:67%;" />

#### 检测物理射线

<img src="Unity入门-二/image-20230531153432898.png" alt="image-20230531153432898" style="zoom:67%;" />



**绘制线段**

- Scene中显示射线

 <img src="Unity入门-二/image-20230531154612018.png" alt="image-20230531154612018" style="zoom:67%;" />

- 游戏中的射线

 <img src="Unity入门-二/image-20230531154825692.png" alt="image-20230531154825692" style="zoom:67%;" />



### 插值运算

#### Time时间类

**API**

 <img src="Unity入门-二/image-20230531173033800.png" alt="image-20230531173033800" style="zoom:67%;" />



#### Mathf数学类

 <img src="Unity入门-二/image-20230531174106687.png" alt="image-20230531174106687" style="zoom:50%;" />

- 角度与弧度转换
  -  <img src="Unity入门-二/image-20230531174038979.png" alt="image-20230531174038979" style="zoom: 50%;" />



#### 插值运算

确定两个参数A和B，从A平滑过渡到B

 <img src="Unity入门-二/image-20230531174411627.png" alt="image-20230531174411627" style="zoom: 67%;" />

**使用**：

<img src="Unity入门-二/image-20230531174502851.png" alt="image-20230531174502851" style="zoom:67%;" />

 <img src="Unity入门-二/image-20230531174606819.png" alt="image-20230531174606819" style="zoom: 50%;" />

 <img src="Unity入门-二/image-20230531174714699.png" alt="image-20230531174714699" style="zoom:50%;" />

 <img src="Unity入门-二/image-20230531174741231.png" alt="image-20230531174741231" style="zoom:50%;" />



**采用累加插值系数解决前快后慢**

 <img src="Unity入门-二/image-20230531212916531.png" alt="image-20230531212916531" style="zoom:67%;" />



**其他插值API**

- 向量插值
  -  <img src="Unity入门-二/image-20230531213456923.png" alt="image-20230531213456923" style="zoom:50%;" />
- 四元数插值
  -  <img src="Unity入门-二/image-20230531213735772.png" alt="image-20230531213735772" style="zoom:50%;" />



### Unity核心类的继承关系

<img src="Unity入门-二/image-20230531214121060.png" alt="image-20230531214121060"  />

Unity安全模式

<img src="unity（2）/image-20230531220339763.png" alt="image-20230531220339763" style="zoom:67%;" />
