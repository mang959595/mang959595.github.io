---
title: Linux网络编程
date: 2023-02-16 03:37:50
tags: 网络编程
categories: 
- C++开发
- 高性能服务器项目
---

<br>

# 第四章 Linux网络编程

---

<br>

## 4.1 计算机网络相关知识

<br>

### 4.1.1 网络结构模式

#### C/S结构

<!--more-->

**简介**

- 服务器 - 客户机，即 Client - Server（C/S）结构。**C/S 结构通常采取两层结构。服务器负责数据的管理，客户机负责完成与用户的交互任务。**客户机是因特网上访问别人信息的机器，服务器则是提供信息供人访问的计算机。
- **客户机通过局域网与服务器相连，接受用户的请求，并通过网络向服务器提出请求，对数据库进行操作。服务器接受客户机的请求，将数据提交给客户机，客户机将数据进行计算并将结果呈现给用户。**服务器还要提供完善安全保护及对数据完整性的处理等操作，并允许多个客户机同时访问服务器，这就对服务器的硬件处理数据能力提出了很高的要求。
- 在C/S结构中，**应用程序分为两部分：服务器部分和客户机部分。**服务器部分是多个用户共享的信息与功能，执行后台服务，如控制共享数据库的操作等；客户机部分为用户所专有，负责执行前台功能，在出错提示、在线帮助等方面都有强大的功能，并且可以在子程序间自由切换。

<br>

**优点**

1. 能充分发挥客户端 PC 的处理能力，**很多工作可以在客户端处理后再提交给服务器，**所以 C/S 结构**客户端响应速度快**；
2. 操作界面漂亮、形式多样，可以充分满足客户自身的**个性化**要求；
3. C/S 结构的**管理信息系统具有较强的事务处理能力**，能实现复杂的业务流程；
4. **安全性较高**，C/S 一般面向相对固定的用户群，**程序更加注重流程**，它可以**对权限进行多层次校验**，提供了更安全的存取模式，对信息安全的控制能力很强，一般**高度机密**的信息系统采用 C/S 结构适宜。

<br>

**缺点**

1. **客户端需要安装专用的客户端软件**。首先涉及到安装的工作量，其次任何一台电脑出问题，如病毒、硬件损坏，都需要进行安装或维护。系统软件升级时，每一台客户机需要重新安装，其维护和升级成本非常高；
2. **对客户端的操作系统一般也会有限制，不能够跨平台**

<br>

<br>

#### B/S 结构

**简介**

- B/S 结构（Browser/Server，浏览器/服务器模式），**是WEB兴起后的一种网络结构模式，WEB浏览器是客户端最主要的应用软件。**这种模式**统一了客户端**，将系统功能实现的**核心部分集中到服务器上**，简化了系统的开发、维护和使用。客户机上只要安装一个浏览器，如 Firefox 或 InternetExplorer，服务器安装 SQL Server、Oracle、MySQL 等数据库。浏览器通过 Web Server 同数据库进行数据交互

<br>

**优点**

- B/S 架构最大的优点是总体拥有**成本低、维护方便、 分布性强、开发简单**，可以不用安装任何专门的软件就能实现在任何地方进行操作，客户端零维护，系统的扩展非常容易，只要有一台能上网的电脑就能使用。

<br>

**缺点**

1. **通信开销大、系统和数据的安全性较难保障;**
2. 个性特点明显降低，无法实现具有个性化的功能要求；
3. 协议一般是**固定**的：**http/https**（无法输出大数据）
4. 客户端服务器端的交互是**请求-响应模式**，通常**动态刷新**页面，**响应速度明显降低**。

<br>

<br>

<br>

### 4.1.2 MAC、IP、端口

#### 网卡

（网卡分为 以太网卡 和 无线网卡 ）

- 网卡是一块被设计用来允许计算机在计算机网络上进行通讯的计算机硬件，又称为 **网络适配器** 或 **网络接口卡NIC** 。
- 其**拥有 MAC 地址**，属于 OSI 模型的**第 2 层**，它使得用户可以通过电缆或无线相互连接。
- 每一个网卡都有一个被称为 MAC 地址的独一无二的 **48 位**串行号。
- 网卡的主要功能：
  - 数据的封装与解封装
  - 链路管理
  - 数据编码与译码

<br>

#### MAC地址

- Media Access Control Address
- 直译为 **媒体存取控制位址** ，也称为 **局域网地址**、**以太网地址**、**物理地址** 或 **硬件地址** ，它是一个用来确认网络设备位置的位址，由网络设备制造商生产时烧录在网卡中
- 在 OSI 模型中，**第三层网络层负责IP地址，第二层数据链路层则负责MAC位址** 
- MAC 地址用于在网络中**唯一标识一个网卡**。一台设备若有一或多个网卡，则每个网卡都需要并会有一个唯一的 MAC 地址。
- MAC 地址的长度为 **48 位（6个字节）**，通常表示为 12 个 16 进制数，如：00-16-EA-AE-3C-40 就是一个MAC 地址。
  - 其中前 3 个字节，16 进制数 00-16-EA 代表网络硬件制造商的编号，它由IEEE（电气与电子工程师协会）分配
  - 而后 3 个字节，16进制数 AE-3C-40 代表该制造商所制造的某个网络产品（如网卡）的系列号。
  - 只要不更改自己的 MAC 地址，MAC 地址在世界是唯一的。形象地说，MAC 地址就如同身份证上的身份证号码，具有唯性。

<br>

#### IP

**简介**

​	IP（Internet Protocol）协议是为计算机网络相互连接进行通信而设计的协议。在 **因特网** 中，它是能使连接到网上的所有计算机网络实现相互通信的一套规则，规定了计算机在因特网上进行通信时应当遵守的规则。任何厂家生产的计算机系统，只要遵守IP协议就可以与因特网互连互通。

​	各个厂家生产的网络系统和设备，如以太网、分组交换网等，它们相互之间不能互通，不能互通的主要原因是因为它们所传送数据的基本单元（技术上称之为“帧”）的格式不同。IP 协议实际上是一套由软件程序组成的协议软件，它把各种不同“**帧**”统一转换成“**IP 数据报**”格式，这种转换是因特网的一个最重要的特点，使所有各种计算机都能在因特网上实现互通，即具有“开放性”的特点。正是因为有了 IP 协议，因特网才得以迅速发展成为世界上最大的、开放的计算机通信网络。因此，IP 协议也可以叫做“**因特网协议**”。

<br>

- **IP地址**（Internet Protocol Address）是指**互联网协议地址**，又译为**网际协议地址**。IP 地址是IP协议提供的一种统一的地址格式，**它为互联网上的每一个网络和每一台主机分配一个逻辑地址，以此来屏蔽物理地址的差异**

- IP 地址是一个 **32 位的二进制数**，通常被分割为 **4 个“ 8 位二进制数”**（也就是 4 个字节）。

<br>

**IP地址类型**

![image-20230210225815319](第四章Linux网络编程/image-20230210225815319.png)

补充：

- D类IP地址：D 类 IP 地址在历史上被叫做多播地址（multicast address），即组播地址。在以太网中，多播地址命名了一组应该在这个网络中应用接收到一个分组的站点。多播地址的最高位必须是 “1110”，范围从224.0.0.0 - 239.255.255.255。
- **特殊的网址**（E类）：
  - 每一个字节都为 0 的地址（ “0.0.0.0” ）对应于当前主机；
  - IP 地址中的每一个字节都为 1 的 IP 地址（ “255.255.255.255” ）是当前子网的广播地址；
  - IP 地址中凡是以 “11110” 开头的 E 类 IP 地址都保留用于将来和实验使用。
  - IP地址中不能以十进制 “127” 作为开头，该类地址中数字 127.0.0.1 到 127.255.255.255 用于回路测试，如：127.0.0.1可以代表本机IP地址

<br>

**子网掩码**

- 子网掩码（subnet mask）又叫网络掩码、地址掩码、子网络遮罩，它是一种用来指明一个 IP 地址的哪些位标识的是主机所在的子网，以及哪些位标识的是主机的位掩码。子网掩码不能单独存在，它必须结合 IP 地址一起用。子网掩码只有一个作用，就是**将某个 IP 地址划分成 网络地址 和 主机地址 两部分**。
- 子网掩码是一个 32 位地址，用于屏蔽 IP 地址的一部分以区别网络标识和主机标识，并说明该 IP地址是在局域网上，还是在广域网上。
- 子网掩码是在 IPv4 地址资源紧缺的背景下为了解决 IP 地址分配而产生的虚拟 IP 技术，通过子网掩码将A、B、C 三类地址划分为若干子网，从而显著提高了 IP 地址的分配效率，有效解决了 IP 地址资源紧张的局面。另一方面，在企业内网中为了更好地管理网络，网管人员也利用子网掩码的作用，人为地将一个较大的企业内部网络划分为更多个小规模的子网，再利用三层交换机的路由功能实现子网互联，从而有效解决了网络广播风暴和网络病毒等诸多网络管理方面的问题。

<br>

<br>

#### 端口

> 端口 就是一块缓冲区，有 读缓冲 和 写缓冲 

**简介**

- 端口 是英文 port 的意译，可以认为是设备与外界通讯交流的出口。端口可分为 **虚拟端口** 和 **物理端口** ，
  - **虚拟端口** 指**计算机内部或交换机路由器内的端口**，**不可见**，是特指**TCP/IP协议中的端口**，是**逻辑意义上**的端口。例如计算机中的 80 端口、21 端口、23 端口等。
  - **物理端口** 又称为**接口**，**是可见端口**，计算机背板的 RJ45 网口，交换机路由器集线器等 RJ45 端口。电话使用 RJ11 插口也属于物理端口的范畴。
- 如果把 IP 地址比作一间房子，端口就是出入这间房子的门。真正的房子只有几个门，但是一个 IP地址的端口可以有 **65536**（即：2^16）个之多！端口是通过端口号来标记的，端口号只有整数，范围是从 **0 到 65535**（2^16-1）。

<br>

**端口类型**

- **周知端口** Well Known Ports ：
  - 也叫知名端口、公认端口或者常用端口，范围从 **0 到 1023**，它们紧密绑定于一些特定的服务。
  - 例如 **80 端口分配给 WWW 服务，21 端口分配给 FTP 服务，23 端口分配给Telnet服务**等等。
  - 网络服务是可以使用其他端口号的，比如使用“8080” 作为 WWW服务的端口；但是有些系统协议使用固定的端口号，比如 139 端口专门用于 NetBIOS 与 TCP/IP 之间的通信

- **注册端口** Registered Ports ：
  - 端口号从 **1024 到 49151**，它们松散地绑定于一些服务，**分配给用户进程或应用程序**。
  - 这些端口在没有被服务器资源占用的时候，可以给用户端动态选用为源端口。
- **动态端口** / **私有端口** Dynamic Ports/Private Ports ：
  - 动态端口的范围是从 **49152 到 65535**。之所以称为动态端口，是因为它一般不固定分配某种服务，而是**动态分配**。

<br>

<br>

<br>

<br>

### 4.1.3 网络模型

#### OSI七层模型

Open System Interconnection

![image-20230210233638076](第四章Linux网络编程/image-20230210233638076.png)



**物理层**：主要定义物理设备标准，如网线的接口类型、光纤的接口类型、各种传输介质的传输速率等。它的主要作用是传输比特流（就是由1、0转化为电流强弱来进行传输，到达目的地后再转化为1、0，也就是我们常说的数模转换与模数转换）。这一层的数据叫做比特。

<br>

**数据链路层**：建立逻辑连接、进行硬件地址寻址、差错校验等功能。定义了如何让格式化数据以帧为单位进行传输，以及如何让控制对物理介质的访问。将比特组合成字节进而组合成帧，用MAC地址访问介质。

<br>

**网络层**：进行逻辑地址寻址，在位于不同地理位置的网络中的两个主机系统之间提供连接和路径选择。Internet的发展使得从世界各站点访问信息的用户数大大增加，而网络层正是管理这种连接的层。

<br>

**传输层**：定义了一些传输数据的协议和端口号（ WWW 端口 80 等），如：TCP（传输控制协议，传输效率低，可靠性强，用于传输可靠性要求高，数据量大的数据），UDP（用户数据报协议，与TCP 特性恰恰相反，用于传输可靠性要求不高，数据量小的数据，如 QQ 聊天数据就是通过这种方式传输的）。 主要是将从下层接收的数据进行分段和传输，到达目的地址后再进行重组。常常把这一层数据叫做段。

<br>

**会话层**：通过传输层（端口号：传输端口与接收端口）建立数据传输的通路。主要在你的系统之间发起会话或者接受会话请求。

<br>

**表示层**：**数据的表示、安全、压缩。**主要是进行对接收的数据进行解释、加密与解密、压缩与解压缩等（也就是把计算机能够识别的东西转换成人能够能识别的东西（如图片、声音等）。

<br>

**应用层**：网络服务与最终用户的一个接口。这一层为用户的应用程序（例如电子邮件、文件传输和终端仿真）提供网络服务

<br>

<br>

#### TCP/IP 四层模型

![image-20230210233916419](第四章Linux网络编程/image-20230210233916419.png)

现在 Internet（因特网）使用的**主流协议族是 TCP/IP 协议族**，它是一个分层、多协议的通信体系。TCP/IP协议族是一个四层协议系统，**自底而上分别是数据链路层、网络层、传输层和应用层**。

<br>

**简化OSI七层模型**

1. **应用层、表示层、会话层** 三个层次提供的服务相差不是很大，所以在 TCP/IP 协议中，它们被合并为 **应用层** 一个层次。
2. 由于 **传输层、网络层** 在网络协议中的地位十分重要，所以在 TCP/IP 协议中它们被作为**独立**的两个层次。
3. 因为 **数据链路层、物理层** 的内容相差不多，所以在 TCP/IP 协议中它们被归并在 **网络接口层** 一个层次里。

<br>

**四层介绍**

- **应用层**：应用层是 TCP/IP 协议的第一层，是直接为应用进程提供服务的。
  - 对不同种类的应用程序它们会根据自己的需要来使用应用层的不同协议，邮件传输应用使用了 SMTP 协议、万维网应用使用了 HTTP 协议、远程登录服务应用使用了有 TELNET 协议。
  - 应用层还能**加密、解密、格式化数据**。
  - 应用层可以**建立或解除与其他节点的联系**，这样可以充分节省网络资源。
- **传输层**：作为 TCP/IP 协议的第二层，运输层在整个 TCP/IP 协议中起到了中流砥柱的作用。且在运输层中， **TCP 和 UDP** 也同样起到了中流砥柱的作用。
- **网络层**：网络层在 TCP/IP 协议中的位于第三层。在 TCP/IP 协议中网络层可以进行**网络连接的建立和终止**以及 **IP 地址的寻找**等功能。
- **网络接口层**：在 TCP/IP 协议中，网络接口层位于第四层。由于网络接口层兼并了物理层和数据链路层所以，网络接口层既是**传输数据的物理媒介**，也可以为网络层**提供一条准确无误的线路**。

<br>

<br>

**比较**

![image-20230210235626139](第四章Linux网络编程/image-20230210235626139.png)

<br>

<br>

<br>

<br>

### 4.1.4 协议

**简介**

​	**协议**，网络协议的简称，网络协议是通信计算机双方必须共同遵从的一组约定。

- 如怎么样建立连接、怎么样互相识别等。只有遵守这个约定，计算机之间才能相互通信交流。
- 它的三要素是：**语法、语义、时序**。
- 为了使数据在网络上从源到达目的，网络通信的参与方必须遵循相同的规则，这套规则称为协议（protocol），它最终体现为在网络上传输的**数据包的格式**。协议往往分成几个层次进行定义，**分层定义**是为了<u>使某一层协议的改变不影响其他层次的协议</u>。

<br>

**常见协议**

- 应用层：
  - FTP协议（File Transfer Protocol 文件传输协议）
  - HTTP协议（Hyper Text Transfer Protocol 超文本传输协议）
  - NFS（Network File System 网络文件系统）。（SSH协议端口号是22）

- 传输层：
  - TCP协议（Transmission Control Protocol 传输控制协议）
  - UDP协议（User Datagram Protocol 用户数据报协议）。
- 网络层：
  - IP 协议（Internet Protocol 因特网互联协议）
  - ICMP 协议（Internet Control Message Protocol 因特网控制报文协议）
  - IGMP 协议（Internet Group Management Protocol 因特网组管理协议）。
- 网络接口层：
  - ARP协议（Address Resolution Protocol 地址解析协议）
  - RARP协议
    （Reverse Address Resolution Protocol 反向地址解析协议）

<br>

<br>

#### UDP报

![image-20230211001006533](第四章Linux网络编程/image-20230211001006533.png)

![image-20230211003426471](第四章Linux网络编程/image-20230211003426471.png)

<br>

#### TCP报

![image-20230211001202108](第四章Linux网络编程/image-20230211001202108.png)

![image-20230211003839373](第四章Linux网络编程/image-20230211003839373.png)

flag：
![image-20230211004511396](第四章Linux网络编程/image-20230211004511396.png)

![image-20230211004527855](第四章Linux网络编程/image-20230211004527855.png)

<br>

#### IP报

![image-20230211003610812](第四章Linux网络编程/image-20230211003610812.png)

![image-20230211005238765](第四章Linux网络编程/image-20230211005238765.png)

<br>

#### 以太网帧

![image-20230211003524443](第四章Linux网络编程/image-20230211003524443.png)

类型：0x800 IP，0x806 ARP，0x835 RARP

<br>

#### ARP协议

![image-20230211003731026](第四章Linux网络编程/image-20230211003731026.png)

![image-20230211003742083](第四章Linux网络编程/image-20230211003742083.png)

<br>

<br>

#### 通信过程

**封装**

​	应用程序数据在发送到物理网络上之前，将沿着协议栈从上往下依次传递。每层协议都将在上层数据的基础上加上自己的头部信息（有时还包括尾部信息），以实现该层的功能，这个过程就称为封装。

> ![封装](第四章Linux网络编程/image-20230211010042659.png)

<br>

**分用**

​	当帧到达目的主机时，将沿着协议栈自底向上依次传递。各层协议依次处理帧中本层负责的头部数据，以获取所需的信息，并最终将处理后的帧交给目标应用程序。这个过程称为分用（demultiplexing）。分用是依靠头部信息中的类型字段实现的。

>![分用](第四章Linux网络编程/image-20230211010337835.png)

<br>

**封装与解封装案例**

（数据链路层还有 帧尾 的校验码）

![封装与解封装](第四章Linux网络编程/image-20230211010845923.png)

<br>

<br>

#### ARP协议的分析

封装过程中，从网络层到数据链路层，需要添加的以太网帧头含有 **目的mac地址** 字段

**问题**：目前知道 目的IP地址 ，要从何获取目的mac地址？ —— 利用ARP协议（通过 IP 获取 mac）

先查找本地 地址解析表 ，如果没有就如下：

![arp](第四章Linux网络编程/image-20230211011936900.png)

- 广播发送
- 单播应答

<br>

**帧结构**

![ARP报文](第四章Linux网络编程/image-20230211020206256.png)

![加上帧头（请求包）](第四章Linux网络编程/image-20230211020650494.png)

> ![image-20230211003731026](第四章Linux网络编程/image-20230211003731026.png)

<br>

<br>

### 4.1.5 Socket套接字

**socket**（套接字），就是对 **网络中不同主机上的应用进程之间** 进行**双向通信**的**端点**的抽象。一个套接字就是网络上进程通信的一端，提供了应用层进程利用网络协议交换数据的机制。从所处的地位来讲，套接字上联应用进程，下联网络协议栈，是应用程序通过网络协议进行通信和交互的接口。

<br>

socket 可以看成是两个网络**应用程序**进行通信时，各自**通信连接中的端点**，这是一个**逻辑上的概念**。它是网络环境中进程间通信的 API，也是可以 **被命名** 和 **寻址** 的通信端点，使用中的每一个套接字都有其类型和一个与之相连进程。通信时其中一个网络应用程序将要传输的一段信息写入它所在主机的 socket 中，该 socket 通过与网络接口卡（NIC）相连的传输介质将这段信息送到另外一台主机的 socket 中，使对方能够接收到这段信息。socket 是由 **IP** 和 **端口** 结合的，提供**向应用层进程传送数据包**的机制。

<br>

socket 本身有“**插座**”的意思，在 Linux 环境下，用于表示进程间网络通信的特殊文件类型。本质为内核借助 **缓冲区** 形成的 **伪文件** 。既然是文件，那么理所当然的，我们可以使用 **文件描述符** 引用套接字。与管道类似的，<u>Linux 系统将其封装成文件的目的是为了统一接口</u>，使得读写套接字和读写文件的操作一致。<u>区别是管道主要应用于本地进程间通信，而套接字多应用于网络进程间数据的传递。</u>

<br>

![image-20230211023108406](第四章Linux网络编程/image-20230211023108406.png)

<br>

套接字通信分两部分：
- **服务器端**：被动接受连接，一般不会主动发起连接
- **客户端**：主动向服务器发起连接

> socket是一套通信的接口，Linux 和 Windows 都有，但是有一些细微的差别

<br>

<br>

### 4.1.6 字节序

> 现代 CPU 的累加器一次都能装载（至少）4 字节（这里考虑 32 位机），即一个整数。
>
> 这 4 字节在内存中排列的顺序将影响它被累加器装载成的整数的值，这就是字节序问题。
>
> 通信双方交流的信息单元（比特、字节、字、双字等等）应该以什么样的顺序进行传送。

<br>

字节序分为大端字节序（Big-Endian） 和小端字节序（Little-Endian）。

- **大端** 字节序是指 一个整数 的 **最高位字节**（0x后面最左边的数字）存储在 **内存** 的 **低地址** 处 ，低位字节（0 ~ 7 bit）存储在内存的高地址处；
  ![image-20230212223224461](第四章Linux网络编程/image-20230212223224461.png)

- **小端** 字节序则是指整数的高位字节存储在内存的高地址处，而低位字节则存储在内存的低地址处。

  ![image-20230212223233439](第四章Linux网络编程/image-20230212223233439.png)

<br>

**大部分计算机采用小端序**

<br>

#### 检测主机字节序

利用 union

```c
// 通过代码检测当前主机的字节序
#include <stdio.h>

int main() {
	// 定义联合体
    union {
        short value;    // 2字节
        char bytes[sizeof(short)];  // char[2]
    } test;

    test.value = 0x0102;
    if((test.bytes[0] == 1) && (test.bytes[1] == 2)) {
        printf("大端字节序\n");
    } else if((test.bytes[0] == 2) && (test.bytes[1] == 1)) {
        printf("小端字节序\n");
    } else {
        printf("未知\n");
    }

    return 0;
}

```

<br>

<br>

#### 字节序转换

当格式化的数据在两台使用不同字节序的主机之间直接传递时：

**发送端总是把要发送的数据转换成大端字节序数据后再发送，**而接收端知道对方传送过来的数据总是采用大端字节序，所以接收端可以根据自身采用的字节序决定是否对接收到的数据进行转换。

<br>

**网络字节顺序**：

 TCP/IP 中规定好的一种数据表示格式，它与具体的 CPU 类型、操作系统等无关，从而可以保证数据在不同主机之间传输时能够被正确解释，**网络字节顺序采用大端排序方式**。

<br>

BSD Socket提供了封装好的转换接口，方便程序员使用。包括

- 从主机字节序 到 网络字节序 的转换函数htons、htonl；

- 从网络字节序到主机字节序的转换函数：ntohs、ntohl。

<br>

```c
/*
	h - host 主机，主机字节序
	to - 转换成什么
	n - network 网络字节序
	
	s - short unsigned short	，端口 
	l - long unsigned int	，IP

*/

#include <arpa/inet.h>

// 端口	short unsigned short
uint16_t htons(uint16_t hostshort); // 主机字节序 - 网络字节序
uint16_t ntohs(uint16_t netshort); // 网络字节序 - 主机字节序

// IP long unsigned int
uint32_t htonl(uint32_t hostlong); // 主机字节序 - 网络字节序
uint32_t ntohl(uint32_t netlong); // 网络字节序 - 主机字节序


```

<br><br>

<br>

**实例**

```c
#include <stdio.h>
#include <arpa/inet.h>

int main() {

    // htons 转换端口
    unsigned short a = 0x0102;
    printf("a : %x\n", a);
    unsigned short b = htons(a);
    printf("b : %x\n", b);

    printf("=======================\n");

    // htonl  转换IP
    char buf[4] = {192, 168, 1, 100};
    int num = *(int *)buf;  // 先取数组地址，在对其进行int类型转换，再解引用
    int sum = htonl(num);
    unsigned char *p = (char *)&sum;

    printf("%d %d %d %d\n", *p, *(p+1), *(p+2), *(p+3));

    printf("=======================\n");

    // ntohl
    unsigned char buf1[4] = {1, 1, 168, 192};
    int num1 = *(int *)buf1;
    int sum1 = ntohl(num1);
    unsigned char *p1 = (unsigned char *)&sum1;
    printf("%d %d %d %d\n", *p1, *(p1+1), *(p1+2), *(p1+3));
    
    // ntohs
    unsigned short c = 0x0201;
    printf("a : %x\n", c);
    unsigned short d = ntohs(c);
    printf("b : %x\n", d);

    return 0;
}

```



<br><br>

<br>

### 4.1.7 socket 地址

socket地址其实是一个结构体，封装端口号和IP等信息

#### 通用socket地址

```c
#include <bits/socket.h>


struct sockaddr {
	sa_family_t sa_family;
	char sa_data[14];
};


typedef unsigned short int sa_family_t;

```



<br>

**sa_family** 成员是 地址族类型（sa_family_t）的变量。地址族类型通常与协议族类型对应。常见的协议族（protocol family，也称 domain）和对应的地址族入下所示：

| 协议族   | 地址族   | 描述             |
| -------- | -------- | ---------------- |
| PF_UNIX  | AF_UNIX  | UNIX本地域协议族 |
| PF_INET  | AF_INET  | TCP/IPv4协议族   |
| PF_INET6 | AF_INET6 | TCP/IPv6协议族   |

> （宏 PF\_* 和 AF_* 都定义在 bits/socket.h 头文件中，且后者与前者有完全相同的值，所以二者通常混用）

<br>

**sa_data** 成员用于存放 **socket 地址值**。但是，不同的协议族的地址值具有不同的含义和长度，如下所示：

| 协议族   | 地址值含义                                                   |
| :------- | ------------------------------------------------------------ |
| PF_UNIX  | 文件的路径名，长度可达108字节                                |
| PF_INET  | 16 bit 端口号 和 32 bit IPv4地址                             |
| PF_INET6 | 16 bit 端口号，32 bit 流标识，128 bit IPv6 地址，32 bit 范围 ID  （共26字节） |

<br>

由上表可知，14 字节的 sa_data 根本无法容纳多数协议族的地址值。因此，Linux 定义了下面这个**新的通用的 socket 地址结构体，这个结构体不仅提供了足够大的空间用于存放地址值，而且是内存对齐的**。

```c
#include <bits/socket.h>

struct sockaddr_storage
{
	sa_family_t sa_family;
	unsigned long int __ss_align;
    //	用来对其内存
	char __ss_padding[ 128 - sizeof(__ss_align) ];	//	具体地址数据
};

typedef unsigned short int sa_family_t;

```

<br>

<br>

#### 专用socket地址

很多网络编程函数诞生早于 IPv4 协议，那时候都使用的是 struct sockaddr 结构体，为了向前兼容，现sockaddr 退化成了（void *）的作用，传递一个地址给函数，至于是 sockaddr_in 还是sockaddr_in6，由地址族确定，然后函数内部再强制类型转化为所需的地址类型。

```c
// TCP/IP 协议族有 sockaddr_in 和 sockaddr_in6 两个专用的 socket 地址结构体，它们分别用于 IPv4 和 IPv6：
#include <netinet/in.h>

struct sockaddr_in
{
	sa_family_t sin_family;	// 地址族
	in_port_t sin_port; 	// 端口
	struct in_addr sin_addr; // IP
    
	/* 对其内存用 */
	unsigned char sin_zero[sizeof (struct sockaddr) - __SOCKADDR_COMMON_SIZE - sizeof (in_port_t) - sizeof (struct in_addr)];
};

struct in_addr
{
	in_addr_t s_addr;	//typedef uint32_t in_addr_t;
};


//	IPv6
struct sockaddr_in6
{
	sa_family_t sin6_family;
	in_port_t sin6_port; /* Transport layer port # */
	uint32_t sin6_flowinfo; /* IPv6 flow information */
	struct in6_addr sin6_addr; /* IPv6 address */
	uint32_t sin6_scope_id; /* IPv6 scope-id */
};


typedef unsigned short uint16_t;
typedef unsigned int uint32_t;
typedef uint16_t in_port_t;
typedef uint32_t in_addr_t;

#define __SOCKADDR_COMMON_SIZE (sizeof (unsigned short int))

```

<br>

![image-20230213070943568](第四章Linux网络编程/image-20230213070943568.png)

**所有专用 socket 地址（以及 sockaddr_storage）类型的变量在实际使用时都需要转化为通用 socket 地址类型 sockaddr（强制转化即可）**，因为所有 socket 编程接口使用的地址参数类型都是 sockaddr

<br>

<br>

<br>

### 4.1.8 IP地址转换

**点分十进制字符串** 表示的 **IPv4** 地址和用 **网络字节序整数** 表示的 **IPv4** 地址之间的转换：

```c
#include <arpa/inet.h>

// 地址字符串 转 网络字节序整数
in_addr_t inet_addr(const char *cp);

// 地址字符串 转 网络字节序整数
int inet_aton(const char *cp, struct in_addr *inp);

// 网络字节序整数 转 地址字符串
char *inet_ntoa(struct in_addr in);

```

<br>

**新版的函数接口**：IPv4，IPv6

```c
#include <arpa/inet.h>

// p:点分十进制的IP字符串，n:表示network，网络字节序的整数

// 地址字符串 转 网络字节序整数
int inet_pton(int af, const char *src, void *dst);
af:地址族： AF_INET AF_INET6
src:需要转换的点分十进制的IP字符串
dst:转换后的结果保存在这个里面
    
    
// 网络字节序整数 转 地址字符串
const char *inet_ntop(int af, const void *src, char *dst, socklen_t size);
af:地址族： AF_INET AF_INET6
src: 要转换的ip的整数的地址
dst: 转换成IP地址字符串保存的地方
size：第三个参数的大小（数组的大小）
返回值：返回转换后的数据的地址（字符串），和 dst 是一样的

```

<br>

<br>

**案例**

```c
#include <stdio.h>
#include <arpa/inet.h>

int main() {

    // 创建一个ip字符串,点分十进制的IP地址字符串
    char buf[] = "192.168.1.4";
    unsigned int num = 0;

    // 将点分十进制的IP字符串转换成网络字节序的整数
    inet_pton(AF_INET, buf, &num);
    unsigned char * p = (unsigned char *)&num;
    printf("%d %d %d %d\n", *p, *(p+1), *(p+2), *(p+3));


    // 将网络字节序的IP整数转换成点分十进制的IP字符串
    char ip[16] = "";
    const char * str =  inet_ntop(AF_INET, &num, ip, 16);
    printf("str : %s\n", str);
    printf("ip : %s\n", ip);
    printf("%d\n", ip == str);

    return 0;
}

```

<br>

<br>

<br><br>

<br>

## 4.2 TCP通信

> UDP于TCP对比
>
> ---
>
> **UDP**: 
> 	**用户数据报** 协议，面向**无连接**，可以**单播，多播，广播**， 面向数据报，不可靠
>
> **TCP**: 
> 	**传输控制** 协议，面向**连接**的，可靠的，基于**字节流**，仅支持**单播**传输

|              |              UDP               |            TCP             |
| ------------ | :----------------------------: | :------------------------: |
| 是否创建连接 |             无连接             |          面向连接          |
| 是否可靠     |             不可靠             |           可靠的           |
| 连接对象个数 | 一对一、一对多、多对一、多对多 |           一对一           |
| 传输的方式   |           面向数据报           |         面向字节流         |
| 首部开销     |            8个字节             |        最少20个字节        |
| 适用场景     |   实时应用（视频会议，直播）   | 可靠性高的应用（文件传输） |

<br>

<br>

### 4.2.1 TCP通信流程

**服务器端** （被动接受连接的角色）

1. 创建一个 **用于监听** 的套接字
	- 监听：监听有客户端的连接
	- 套接字：这个套接字其实就是一个文件描述符
2. 将这个监听文件描述符和本地的IP和端口绑定（IP和端口就是服务器的地址信息）
	- 客户端连接服务器的时候使用的就是这个IP和端口
3. 设置监听，监听的fd开始工作
4. 阻塞等待，当有客户端发起连接，解除阻塞，接受客户端的连接，会得到一个和客户端通信的套接字（fd）
5. 通信
	- 接收数据
	- 发送数据
6. 通信结束，断开连接

<br>

**客户端**

1. 创建一个用于通信的套接字（fd）
2. 连接服务器，需要指定连接的服务器的 IP 和 端口
3. 连接成功了，客户端可以直接和服务器通信
	- 接收数据
	- 发送数据
4. 通信结束，断开连接

<br>

> ![image-20230213082358002](第四章Linux网络编程/image-20230213082358002.png)

<br>

<br>

### 4.2.2 Socket函数

```c
#include <sys/types.h>
#include <sys/socket.h>

#include <arpa/inet.h> // 包含了这个头文件，上面两个就可以省略

// 创建套接字
int socket(int domain, int type, int protocol);
	- 功能：创建一个套接字
	- 参数：
		- domain: 协议族
			AF_INET : ipv4
			AF_INET6 : ipv6
			AF_UNIX, AF_LOCAL : 本地套接字通信（进程间通信）
		- type: 通信过程中使用的协议类型
			SOCK_STREAM : 流式协议 TCP
			SOCK_DGRAM : 报式协议  UDP
		- protocol : 具体的一个协议。一般写 0
			- SOCK_STREAM : 流式协议默认使用 TCP
			- SOCK_DGRAM : 报式协议默认使用 UDP
	- 返回值：
		- 成功：返回文件描述符，操作的就是内核缓冲区。
		- 失败：-1


// 将IP、端口绑定到套接字
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen); // socket命名
	- 功能：绑定，将 fd 和本地的IP + 端口进行绑定
	- 参数：
		- sockfd : 通过socket函数得到的文件描述符
		- addr : 需要绑定的socket地址，这个地址封装了ip和端口号的信息
		- addrlen : 第二个参数结构体占的内存大小
            
// 套接字启动监听
int listen(int sockfd, int backlog); // /proc/sys/net/core/somaxconn
	- 功能：监听这个socket上的连接
	- 参数：
		- sockfd : 通过socket()函数得到的文件描述符
		- backlog : 最大连接数， 5即可
            
            
// 接收连接，默认是阻塞的
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
	- 功能：接收客户端连接，默认是一个阻塞的函数，阻塞等待客户端连接
	- 参数：
		- sockfd : 用于监听的文件描述符
		- addr : 传出参数，记录了连接成功后客户端的地址信息（ip，port）
		- addrlen : 指定第二个参数的对应的内存大小
	- 返回值：
		- 成功 ：用于通信的文件描述符
		- -1 ： 失败

            
            
// 客户端发起连接（参数有点像 bind ）
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
	- 功能： 客户端连接服务器
	- 参数：
		- sockfd : 用于通信的文件描述符
		- addr : 客户端要连接的服务器的地址信息
		- addrlen : 第二个参数的内存大小
	- 返回值：成功 0， 失败 -1

            
            
ssize_t write(int fd, const void *buf, size_t count); // 写数据
ssize_t read(int fd, void *buf, size_t count); // 读数据



```



<br>

<br>

<br>

### 4.2.3 三次握手

<img src="第四章Linux网络编程/image-20230214004936804.png" alt="image-20230214004936804" style="zoom:80%;" />

<br>

- TCP 是一种**面向连接的单播协议**，在发送数据前，通信双方必须在彼此间建立一条连接。所谓的**“连接”**，其实是**客户端和服务器的内存里保存的一份关于对方的信息**，如 IP 地址、端口号等。
- TCP 可以看成是一种**字节流**，它会处理 **IP 层或以下的层** 的 **丢包、重复以及错误** 问题。
- 在连接的建立过程中，双方需要交换一些连接的参数。这些参数可以放在 TCP 头部。

- TCP 提供了一种可靠、面向连接、字节流、传输层的服务，采用 三次握手建立一个连接 。采用 四次挥手来关闭一个连接 。 三次握手的目的是 **保证双方互相之间建立了连接** 。
- 三次握手发生在客户端连接的时候，当 **调用connect()** ，底层会通过TCP协议进行三次握手。
  

<br>

**TCP 头**

![image-20230214002211867](第四章Linux网络编程/image-20230214002211867.png)

<br>

- **16 位端口号**（port number）：告知主机报文段是来自哪里（**源端口**）以及传给哪个上层协议或应用程序（**目的端口**）的。进行 TCP 通信时，客户端通常使用系统自动选择的临时端口号。
- **32 位序号**（sequence number）：一次 TCP 通信（从 TCP 连接建立到断开）过程中某一个传输方向上的字节流的每个字节的编号。假设主机 A 和主机 B 进行 TCP 通信，A 发送给 B 的第一个TCP 报文段中，序号值被系统初始化为某个随机值 ISN（Initial Sequence Number，初始序号值）。那么在该传输方向上（从 A 到 B），后续的 TCP 报文段中序号值将被系统设置成 ISN 加上该报文段所携带数据的第一个字节在整个字节流中的偏移。例如，某个 TCP 报文段传送的数据是字节流中的第 1025 ~ 2048 字节，那么该报文段的序号值就是 ISN + 1025。另外一个传输方向（从B 到 A）的 TCP 报文段的序号值也具有相同的含义。
- **32 位确认号**（acknowledgement number）：用作对另一方发送来的 TCP 报文段的响应。其值是收到的 TCP 报文段的序号值 + 标志位长度（SYN，FIN） + 数据长度 。假设主机 A 和主机 B 进行TCP 通信，那么 A 发送出的 TCP 报文段不仅携带自己的序号，而且包含对 B 发送来的 TCP 报文段的确认号。反之，B 发送出的 TCP 报文段也同样携带自己的序号和对 A 发送来的报文段的确认序
- **4 位头部长度**（head length）：标识该 TCP 头部有多少个 32 bit(4 字节)。因为 4 位最大能表示15，所以 TCP 头部最长是60 字节。
- **6 位标志位**包含如下几项：
  - URG 标志，表示紧急指针（urgent pointer）是否有效。
  - ACK 标志，表示确认号是否有效。我们称携带 ACK 标志的 TCP 报文段为确认报文段。
  - PSH 标志，提示接收端应用程序应该立即从 TCP 接收缓冲区中读走数据，为接收后续数据腾出空间（如果应用程序不将接收到的数据读走，它们就会一直停留在 TCP 接收缓冲区中）。
  - RST 标志，表示要求对方重新建立连接。我们称携带 RST 标志的 TCP 报文段为 **复位** 报文段。
  - SYN 标志，表示请求建立一个连接。我们称携带 SYN 标志的 TCP 报文段为 **同步** 报文段。
  - FIN 标志，表示通知对方本端要关闭连接了。我们称携带 FIN 标志的 TCP 报文段为 **结束** 报文段。
- **16 位窗口大小**（window size）：是 TCP 流量控制的一个手段。这里说的窗口，指的是 **接收通告窗口** （Receiver Window，RWND）。它告诉对方本端的 TCP 接收缓冲区还能容纳多少字节的数据，这样对方就可以控制发送数据的速度。
- **16 位校验和**（TCP checksum）：由发送端填充，接收端对 TCP 报文段执行 CRC 算法以校验TCP 报文段在传输过程中是否损坏。注意，这个校验不仅包括 TCP 头部，也包括数据部分。这也是 TCP 可靠传输的一个重要保障。
- **16 位紧急指针**（urgent pointer）：是一个正的偏移量。它和序号字段的值相加表示最后一个紧急数据的下一个字节的序号。因此，确切地说，这个字段是紧急指针相对当前序号的偏移，不妨称之为紧急偏移。TCP 的紧急指针是发送端向接收端发送紧急数据的方法。

<br>

**三次握手**

![image-20230214004856526](第四章Linux网络编程/image-20230214004856526.png)



**传输数据**

![image-20230214004912780](第四章Linux网络编程/image-20230214004912780.png)

<br>

**小结**

第一次握手：
	1.客户端将SYN标志位置为1
	2.生成一个随机的32位的序号seq=J ， 这个序号后边是可以携带数据（数据的大小）
第二次握手：
	1.服务器端接收客户端的连接： ACK=1
	2.服务器会回发一个确认序号： ack = 客户端的序号 + 数据长度 + SYN/FIN(按一个字节算)
	3.服务器端会向客户端发起连接请求： SYN=1
	4.服务器会生成一个随机序号：seq = K
第三次握手：
	1.客户单应答服务器的连接请求：ACK=1
	2.客户端回复收到了服务器端的数据：ack=服务端的序号 + 数据长度 + SYN/FIN(按一个字节算)

<br><br>

### 4.2.4 滑动窗口

**滑动窗口**（Sliding window）是一种 **流量控制** 技术。

> 早期的网络通信中，通信双方不会考虑网络的拥挤情况直接发送数据。由于大家不知道网络拥塞状况，同时发送数据，导致中间节点阻塞掉包，谁也发不了数据，所以就有了滑动窗口机制来解决此问题。

滑动窗口协议是用来 **改善吞吐量** 的一种技术，即**容许发送方在接收任何应答之前传送附加的包** 。**接收方告诉发送方在某一时刻能送多少包**（称 **窗口尺寸**）。

TCP 中采用滑动窗口来进行传输控制，滑动窗口的大小意味着接收方还有多大的缓冲区可以用于接收数据。**发送方可以通过滑动窗口的大小来确定应该发送多少字节的数据**。当滑动窗口为 0 时，发送方一般不能再发送数据报。

> 滑动窗口是 TCP 中实现诸如 **ACK 确认**、**流量控制**、**拥塞控制** 的承载结构。

<br>

- 滑动窗口的大小会随着发送数据和接收数据而变化。
- 通信的双方都有发送缓冲区和接收数据的缓冲区
  

<br>

**图例**

<img src="第四章Linux网络编程/image-20230214235843054.png" alt="image-20230214235843054" style="zoom:80%;" />

<img src="第四章Linux网络编程/image-20230214235856918.png" alt="image-20230214235856918" style="zoom:80%;" />

<br>

![image-20230215000000336](第四章Linux网络编程/image-20230215000000336.png)

> mss: Maximum Segment Size(一条数据的最大的数据量)
>
> win: 滑动窗口

<br>

<br>

<br>

### 4.2.5 四次挥手

- 四次挥手发生在断开连接的时候，在程序中当调用了 close() 会使用TCP协议进行四次挥手。 
- 客户端和服务器端都可以主动发起断开连接，**谁先调用 close() 谁就是发起**。 
- 因为在TCP连接的时候，采用三次握手建立的的**连接是双向**的，在断开的时候需要**双向断开**。

![image-20230215010511553](第四章Linux网络编程/image-20230215010511553.png)

<br>

<img src="第四章Linux网络编程/image-20230215010632884.png" alt="image-20230215010632884" style="zoom:80%;" />



> ack = 客户端的序号 + 数据长度 + SYN/FIN(按一个字节算)

<br>

<br>

<br>

<br>

### 4.2.6 TCP状态转换

![image-20230215160143516](第四章Linux网络编程/image-20230215160143516.png)

<br>

<br>

**三次握手**

- 客户端 SYN_SENT （发送 SYN ）
- 服务端 LISTEN -> SYN_RCVD (接收并发送 SYN 和 ACK 回应)
- 客户端 ESTABLISHED （接收并发送 ACK ）
- 服务端 ESTABLISHED （接收）

**数据传输**

- 读写

**四次握手**

- A: FIN_WAIT_1 （发送 FIN ）
- B: CLOSE_WAIT （接收并发送 ACK ）
- A: FIN_WAIT_2 （接收）
- B: LAST_ACK   （发送 FIN ）
- A: TIME_WAIT  （接收并发送ACK）
- B: （接收）

<br>

<br>

![image-20230215161044162](第四章Linux网络编程/image-20230215161044162.png)

- 红色当作客户端
- 绿色虚线当作服务端
- 黑色是异常

> - 四次握手中，前两次握手完成后，中间还可以继续通信一段时间，再去完成第三第四次握手。
> - TIME_WAIT：最后一次握手（主动方发送ACK）之后，无法立即确定对方是否收到ACK，所以要等待一段时间（**两倍报文段寿命**）来确定（如果对方收到了ACK就不会重发FIN）
> - 2MSL 两倍报文段寿命：主动断开连接的一方, 最后进入一个 TIME_WAIT 状态, 这个状态会持续: 2msl。官方建议: 2分钟, 实际是30s

<br>

<br>

<br>

<br>

## 4.3 并发服务器

### 多进程实现

> 要实现TCP通信服务器处理并发的任务，使用多线程或者多进程来解决。 
>
> 思路： 
>
> 1. 一个父进程，多个子进程 
> 2. 父进程负责等待并接受客户端的连接 
> 3. 子进程：完成通信，接受一个客户端连接，就创建一个子进程用于通信。

<br>

#### 服务端

注意看注释

**子进程处理数据收发**

```c

    // 不断循环等待客户端连接
    while(1) {

        struct sockaddr_in cliaddr;
        int len = sizeof(cliaddr);
        // 接受连接
        int cfd = accept(lfd, (struct sockaddr*)&cliaddr, &len);
        if(cfd == -1) {
            if(errno == EINTR) {
                continue;
            }
            perror("accept");
            exit(-1);
        }

==================================================================
        // 每一个连接进来，创建一个子进程跟客户端通信
        pid_t pid = fork();
        if(pid == 0) {
            // 子进程
            // 获取客户端的信息
            char cliIp[16];
            inet_ntop(AF_INET, &cliaddr.sin_addr.s_addr, cliIp, sizeof(cliIp));
            unsigned short cliPort = ntohs(cliaddr.sin_port);
            printf("client ip is : %s, prot is %d\n", cliIp, cliPort);

            // 接收客户端发来的数据
            char recvBuf[1024];
            while(1) {
                int len = read(cfd, &recvBuf, sizeof(recvBuf));

                if(len == -1) {
                    perror("read");
                    exit(-1);
                }else if(len > 0) {
                    printf("recv client : %s\n", recvBuf);
                } else if(len == 0) {
                    printf("client closed....\n");
                    break;
                }
                // 回声服务器
                write(cfd, recvBuf, strlen(recvBuf) + 1);  // 加1的目的是加上结束符
            }
            
            close(cfd);
            exit(0);    // 退出当前子进程
        }
===================================================================

    }

```

<br>

<br>

**利用信号来回收子进程资源**

注册信号 ( SIGCHLD )

```c

	struct sigaction act;
    act.sa_flags = 0;
    sigemptyset(&act.sa_mask);
    act.sa_handler = recyleChild;
    // 注册信号捕捉
    sigaction(SIGCHLD, &act, NULL);

```

<br>

回调函数

```c
void recyleChild(int arg) {
    while(1) {
        int ret = waitpid(-1, NULL, WNOHANG);
        if(ret == -1) {
            // 所有的子进程都回收了
            break;
        }else if(ret == 0) {
            // 还有子进程活着
            break;
        } else if(ret > 0){
            // 被回收了
            printf("子进程 %d 被回收了\n", ret);
        }
    }
}
```



<br>

<br>

**完整**

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>
#include <wait.h>
#include <errno.h>

void recyleChild(int arg) {
    while(1) {
        int ret = waitpid(-1, NULL, WNOHANG);
        if(ret == -1) {
            // 所有的子进程都回收了
            break;
        }else if(ret == 0) {
            // 还有子进程活着
            break;
        } else if(ret > 0){
            // 被回收了
            printf("子进程 %d 被回收了\n", ret);
        }
    }
}

int main() {

    struct sigaction act;
    act.sa_flags = 0;
    sigemptyset(&act.sa_mask);
    act.sa_handler = recyleChild;
    // 注册信号捕捉
    sigaction(SIGCHLD, &act, NULL);
    

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    if(lfd == -1){
        perror("socket");
        exit(-1);
    }

    struct sockaddr_in saddr;
    saddr.sin_family = AF_INET;
    saddr.sin_port = htons(9999);
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    int ret = bind(lfd,(struct sockaddr *)&saddr, sizeof(saddr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

    // 监听
    ret = listen(lfd, 128);
    if(ret == -1) {
        perror("listen");
        exit(-1);
    }

    // 不断循环等待客户端连接
    while(1) {

        struct sockaddr_in cliaddr;
        int len = sizeof(cliaddr);
        // 接受连接
        int cfd = accept(lfd, (struct sockaddr*)&cliaddr, &len);
        if(cfd == -1) {

            if(errno == EINTR) {
                continue;           // 子进程的信号引发了软中断后，父进程中的accept会失败并设置错误号 EINTR，这时候不能让父进程（监听连接）退出
            }
            
            perror("accept");
            exit(-1);
        }

        // 每一个连接进来，创建一个子进程跟客户端通信
        pid_t pid = fork();
        if(pid == 0) {
            // 子进程
            // 获取客户端的信息
            char cliIp[16];
            inet_ntop(AF_INET, &cliaddr.sin_addr.s_addr, cliIp, sizeof(cliIp));
            unsigned short cliPort = ntohs(cliaddr.sin_port);
            printf("client ip is : %s, prot is %d\n", cliIp, cliPort);

            // 接收客户端发来的数据
            char recvBuf[1024];
            while(1) {
                int len = read(cfd, &recvBuf, sizeof(recvBuf));

                if(len == -1) {
                    perror("read");
                    exit(-1);
                }else if(len > 0) {
                    printf("recv client : %s\n", recvBuf);
                } else if(len == 0) {
                    printf("client closed....\n");
                    break;
                }
                write(cfd, recvBuf, strlen(recvBuf) + 1);  // 加1的目的是加上结束符
            }
            close(cfd);
            exit(0);    // 退出当前子进程
        }

    }
    close(lfd);
    return 0;
}

```



<br>

<br><br>



#### 客户端

```c
// TCP通信的客户端
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>

int main() {

    // 1.创建套接字
    int fd = socket(AF_INET, SOCK_STREAM, 0);
    if(fd == -1) {
        perror("socket");
        exit(-1);
    }

    // 2.连接服务器端
    struct sockaddr_in serveraddr;
    serveraddr.sin_family = AF_INET;
    inet_pton(AF_INET, "192.168.193.128", &serveraddr.sin_addr.s_addr);
    serveraddr.sin_port = htons(9999);
    int ret = connect(fd, (struct sockaddr *)&serveraddr, sizeof(serveraddr));

    if(ret == -1) {
        perror("connect");
        exit(-1);
    }
    
    // 3. 通信
    char recvBuf[1024];
    int i = 0;
    while(1) {
        
        sprintf(recvBuf, "data : %d\n", i++);
        
        // 给服务器端发送数据
        write(fd, recvBuf, strlen(recvBuf)+1);

        int len = read(fd, recvBuf, sizeof(recvBuf));
        if(len == -1) {
            perror("read");
            exit(-1);
        } else if(len > 0) {
            printf("recv server : %s\n", recvBuf);
        } else if(len == 0) {
            // 表示服务器端断开连接
            printf("server closed...");
            break;
        }

        sleep(1);
    }

    // 关闭连接
    close(fd);

    return 0;
}

```

<br>

<br>

**注意**：

如果客户端或者服务端进程提前终止，没有调用close(fd)，那么后续的数据传输会引发错误

比如

<br>

<br>

<br>

<br>

### 多线程实现

**服务端**

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>

struct sockInfo {
    int fd; // 通信的文件描述符
    struct sockaddr_in addr;
    pthread_t tid;  // 线程号
    int pos;
};

struct sockInfo sockinfos[128];

void * working(void * arg) {
    // 子线程和客户端通信   cfd 客户端的信息 线程号
    // 获取客户端的信息
    struct sockInfo * pinfo = (struct sockInfo *)arg;

    char cliIp[16];
    inet_ntop(AF_INET, &pinfo->addr.sin_addr.s_addr, cliIp, sizeof(cliIp));
    unsigned short cliPort = ntohs(pinfo->addr.sin_port);
    printf("client ip is : %s, prot is %d\n", cliIp, cliPort);

    // 接收客户端发来的数据
    char recvBuf[1024];
    while(1) {
        int len = read(pinfo->fd, &recvBuf, sizeof(recvBuf));

        if(len == -1) {
            perror("read");
            exit(-1);
        }else if(len > 0) {
            printf("recv client : %s\n", recvBuf);
        } else if(len == 0) {
            printf("client closed....\n");
            sockinfos[pinfo->pos].fd = -1;
            break;
        }
        write(pinfo->fd, recvBuf, strlen(recvBuf) + 1);
    }
    close(pinfo->fd);
    return NULL;
}

int main() {

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    if(lfd == -1){
        perror("socket");
        exit(-1);
    }

    struct sockaddr_in saddr;
    saddr.sin_family = AF_INET;
    saddr.sin_port = htons(9999);
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    int ret = bind(lfd,(struct sockaddr *)&saddr, sizeof(saddr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

    // 监听
    ret = listen(lfd, 128);
    if(ret == -1) {
        perror("listen");
        exit(-1);
    }

    // 初始化数据
    int max = sizeof(sockinfos) / sizeof(sockinfos[0]);
    for(int i = 0; i < max; i++) {
        bzero(&sockinfos[i], sizeof(sockinfos[i]));
        sockinfos[i].fd = -1;
        sockinfos[i].tid = -1;
    }

    // 循环等待客户端连接，一旦一个客户端连接进来，就创建一个子线程进行通信
    while(1) {

        struct sockaddr_in cliaddr;
        int len = sizeof(cliaddr);
        // 接受连接
        int cfd = accept(lfd, (struct sockaddr*)&cliaddr, &len);

        struct sockInfo * pinfo;
        for(int i = 0; i < max; i++) {
            // 从这个数组中找到一个可以用的sockInfo元素
            if(sockinfos[i].fd == -1) {
                pinfo = &sockinfos[i];
                pinfo->pos = i;
                break;
            }
            if(i == max - 1) {
                sleep(1);
                i = -1;
            }
        }

        pinfo->fd = cfd;
        memcpy(&pinfo->addr, &cliaddr, len);

        // 创建子线程
        pthread_create(&pinfo->tid, NULL, working, pinfo);

        pthread_detach(pinfo->tid);
    }

    close(lfd);
    return 0;
}

```



<br>

<br><br>

<br>

<br>

<br><br>

<br>

<br>

## 4.4 复用

### 4.4.1 半关闭、端口复用、IO多路复用

#### 半关闭

- **主动关闭方** 从发送 FIN 并收到 ACK 后，到收到对方的 FIN 之前，处于 **半关闭** 状态。
- 此时还可以接收对方发送的数据，但不能够再向对方发送数据。

<br>

半关闭不使用 close 实现，而是使用 shutdown ：

```c
#include <sys/socket.h>

int shutdown(int sockfd, int how);

//	sockfd: 需要关闭的socket的描述符
    
//	how: 允许为shutdown操作选择以下几种方式:
//		- SHUT_RD(0): 关闭sockfd上的 读 功能，此选项将不允许sockfd进行读操作。该套接字不再接收数据，任何当前在套接字接受缓冲区的数据将被无声的丢弃掉。
//		- SHUT_WR(1): 关闭sockfd的 写 功能，此选项将不允许sockfd进行写操作。进程不能对此套接字发出写操作。
//		- SHUT_RDWR(2): 关闭sockfd的读写功能。相当于调用shutdown两次：首先是以SHUT_RD,然后以SHUT_WR。

```

- close 中止一个连接，但它只是**减少描述符的引用计数**，并不直接关闭连接，只有**当描述符的引用计数为 0 时才关闭连接**。
- shutdown 不考虑描述符的引用计数，**直接关闭描述符**。也**可选择中止一个方向的连接**，只中止读或只中止写。

> **注意:**
>
> - 如果有多个进程共享一个套接字，close 每被调用一次，计数减 1 ，直到计数为 0 时，也就是所用进程都调用了 close，套接字将被释放。
> - 在多进程中如果一个进程调用了 shutdown(sfd, SHUT_RDWR) 后，其它的进程将无法进行通信。<u>但如果一个进程 close(sfd) 将不会影响到其它进程。</u>

<br>

<br>

#### 端口复用

最常用的用途:

- 防止服务器重启时之前绑定的端口还未释放
- 程序突然退出而系统没有释放端口

> 常看网络相关信息的命令:`netstat`
> 参数：
> 	-a 所有的socket
> 	-p 显示正在使用socket的程序的名称
> 	-n 直接使用IP地址，而不通过域名服务器
>
> ​	...

<br>

**setsockopt函数**

```c
#include <sys/types.h>
#include <sys/socket.h>

// 设置套接字的属性（不仅仅能设置端口复用）
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);
	参数（在UNP（Unix网络编程）书籍中使用）：
		- sockfd : 要操作的文件描述符
		- level : 级别 SOL_SOCKET (使用端口复用的级别)
		- optname : 选项的名称
			- SO_REUSEADDR	IP重用
			- SO_REUSEPORT	端口复用
		- optval : 端口复用的值（整型）
			- 1 : 可以复用
			- 0 : 不可以复用
		- optlen : optval参数的大小
            
            
// 端口复用，设置的时机是在服务器绑定端口之前
	setsockopt();

	bind();

```

<br>

<br>

<br>

#### IO多路复用

又称 **IO多路转接**

- 不是传统意义上的文件IO，而是对套接字读写缓冲区的IO管理
- I/O 多路复用使得程序能 **同时监听多个文件描述符** ，能够提高程序的性能，Linux 下实现 I/O 多路复用的系统调用主要有 **select、poll 和 epoll**。

<br>

**等待模型**

1. **阻塞等待**（阻塞IO模型即 **BIO模型** ）

优点：不占用CPU宝贵的时间片

缺点：
	同一时刻只能处理一个操作，效率低（单进程/单线程） 
	线程或进程的创建消耗系统资源，调度消耗CPU资源（多线程/多进程）

![image-20230215175146862](第四章Linux网络编程/image-20230215175146862.png)

<br>

2. **非阻塞**，**忙轮询**（非阻塞模型即 **NIO模型** ）

优点：提高了程序的执行效率

缺点：占有更多的CPU和系统资源

使用 **IO多路复用**（select，poll，epoll） 来解决这里的缺点,

![image-20230215175852729](第四章Linux网络编程/image-20230215175852729.png)

<br>

<br>

- 第一种IO多路复用技术（select/poll）
  ![image-20230215180146018](第四章Linux网络编程/image-20230215180146018.png)
- 第二种IO多路复用技术（epoll）
  ![image-20230215180630626](第四章Linux网络编程/image-20230215180630626.png)

<br>

<br>

<br>

### 4.4.2 Select

**主要思路：**

1. 首先要构造一个**关于文件描述符的列表**，将要监听的文件描述符添加到该列表中。
2. 调用一个系统函数（即select），**监听该列表中的文件描述符**，<u>直到这些描述符中的一个或者多个进行I/O操作时，该函数才返回</u>。
   - 这个函数是**阻塞的**
   - 函数对文件描述符的检测的操作是由**内核完成的**
3. 在返回时，它会告诉进程**有多少（哪些）描述符**要进行I/O操作。

<br>

```c

#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/select.h>

fd_set 就是文件描述符的 位集，用 位 来表示各个描述符的状态
// sizeof(fd_set) = 128个字节 1024位


// 委托内核监听列表中的文件描述符
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
	- 参数：
		- nfds : 委托内核检测的最大文件描述符的值 + 1
		- readfds : 要检测的文件描述符的读的集合，委托内核检测哪些文件描述符的读的属性
			- 一般检测读操作
			- 对应的是对方发送过来的数据，因为读是被动的接收数据，检测的就是读缓冲区
            - 是一个 传入传出参数
		- writefds : 要检测的文件描述符的写的集合，委托内核检测哪些文件描述符的写的属性
			- 委托内核检测写缓冲区是不是还可以写数据（不满的就可以写）
		- exceptfds : 检测发生异常的文件描述符的集合
		- timeout : 设置的超时时间
			struct timeval {
				long tv_sec; /* seconds */
				long tv_usec; /* microseconds */
			};
			- NULL : 永久阻塞，直到检测到了文件描述符有变化
			- tv_sec = 0 tv_usec = 0， 不阻塞
			- tv_sec > 0 tv_usec > 0， 阻塞对应的时间
	- 返回值 :
		- -1 : 失败
		- >0(n) : 检测的集合中有n个文件描述符发生了变化

            
对位集合的操作         
// 将参数文件描述符fd对应的标志位设置为0（clear）
void FD_CLR(int fd, fd_set *set);


// 将参数文件描述符fd 对应的标志位，设置为1
void FD_SET(int fd, fd_set *set);

// fd_set一共有1024 bit, 全部初始化为0
void FD_ZERO(fd_set *set);

// 判断fd对应的标志位是0还是1， 返回值 ： fd对应的标志位的值，如果是0返回0， 是1返回1
int FD_ISSET(int fd, fd_set *set);

```



<br>

<br>

**Select 工作流程**

<img src="第四章Linux网络编程/image-20230215204459693.png" alt="image-20230215204459693" style="zoom: 67%;" />



<img src="第四章Linux网络编程/image-20230215204514747.png" alt="image-20230215204514747" style="zoom: 67%;" />

<img src="第四章Linux网络编程/image-20230215220639318.png" alt="image-20230215220639318" style="zoom:80%;" />

<br>

**服务端**

- accept之前先设置好fdset并调用select
- 如果select检测出数据变动：
  - 如果是服务端的socketfd有变动，则表示有客户端连接可进行accept
  - 然后再for循环文件描述符列表进行读写处理（如果需要的话）
- fdset用到两个，一个用来给select调用，一个用来维护文件描述符列表
- 注意需要维护一个最大文件描述符

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/select.h>

int main() {

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    struct sockaddr_in saddr;
    saddr.sin_port = htons(9999);
    saddr.sin_family = AF_INET;
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    bind(lfd, (struct sockaddr *)&saddr, sizeof(saddr));

    // 监听
    listen(lfd, 8);

    // 创建一个fd_set的集合，存放的是需要检测的文件描述符
    fd_set rdset, tmp;	// tmp用来给内核检测，rdset用来维护存在的文件描述符
    FD_ZERO(&rdset);
    FD_SET(lfd, &rdset);  // select调用前rdset的某位fd为1表示我们希望内核帮我们检测该fd对应的接收缓存。select调用后rdset对应的fd为1表示该接收缓存接收到数据了，为0表示没接收到数据
    int maxfd = lfd;

    while(1) {

        tmp = rdset;  // 这里要记得

        // 调用select系统函数，让内核帮检测哪些文件描述符有数据
        int ret = select(maxfd + 1, &tmp, NULL, NULL, NULL);
        if(ret == -1) {
            perror("select");
            exit(-1);
        } else if(ret == 0) {
            continue;
        } else if(ret > 0) {
            // 说明检测到了有文件描述符的对应的缓冲区的数据发生了改变
            if(FD_ISSET(lfd, &tmp)) {
                // 表示有新的客户端连接进来了
                struct sockaddr_in cliaddr;
                int len = sizeof(cliaddr);
                int cfd = accept(lfd, (struct sockaddr *)&cliaddr, &len);

                // 将新的文件描述符加入到集合中
                FD_SET(cfd, &rdset);

                // 更新最大的文件描述符
                maxfd = maxfd > cfd ? maxfd : cfd;
            }

            for(int i = lfd + 1; i <= maxfd; i++) {
                if(FD_ISSET(i, &tmp)) {
                    // 说明这个文件描述符对应的客户端发来了数据
                    char buf[1024] = {0};
                    int len = read(i, buf, sizeof(buf));
                    if(len == -1) {
                        perror("read");
                        exit(-1);
                    } else if(len == 0) {
                        printf("client closed...\n");
                        close(i);
                        FD_CLR(i, &rdset);
                    } else if(len > 0) {
                        printf("read buf = %s\n", buf);
                        write(i, buf, strlen(buf) + 1);
                    }
                }
            }

        }

    }
    close(lfd);
    return 0;
}

```

<br>

<br>

**客户端**

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

int main() {

    // 创建socket
    int fd = socket(PF_INET, SOCK_STREAM, 0);
    if(fd == -1) {
        perror("socket");
        return -1;
    }

    struct sockaddr_in seraddr;
    inet_pton(AF_INET, "127.0.0.1", &seraddr.sin_addr.s_addr);
    seraddr.sin_family = AF_INET;
    seraddr.sin_port = htons(9999);

    // 连接服务器
    int ret = connect(fd, (struct sockaddr *)&seraddr, sizeof(seraddr));

    if(ret == -1){
        perror("connect");
        return -1;
    }

    int num = 0;
    while(1) {
        char sendBuf[1024] = {0};
        sprintf(sendBuf, "send data %d", num++);
        write(fd, sendBuf, strlen(sendBuf) + 1);

        // 接收
        int len = read(fd, sendBuf, sizeof(sendBuf));
        if(len == -1) {
            perror("read");
            return -1;
        }else if(len > 0) {
            printf("read buf = %s\n", sendBuf);
        } else {
            printf("服务器已经断开连接...\n");
            break;
        }
        // sleep(1);
        usleep(1000);
    }

    close(fd);

    return 0;
}

```



<br>

<br>

**Select 的缺点**

![image-20230215221709493](第四章Linux网络编程/image-20230215221709493.png)

<br>

<br>

<br>

### 4.4.3 poll

select的改进版

```c
#include <poll.h>
struct pollfd {
	int fd; /* 委托内核检测的文件描述符 */
	short events; /* 委托内核检测文件描述符的什么事件 */
	short revents; /* 文件描述符实际发生的事件 */
};

	struct pollfd myfd;
	myfd.fd = 5;
    myfd.events = POLLIN | POLLOUT;  同时委托内核进行读写操作

int poll(struct pollfd *fds, nfds_t nfds, int timeout);
	- 参数：
		- fds : 是一个struct pollfd 结构体数组，这是一个需要检测的文件描述符的集合
		- nfds : 这个是第一个参数数组中最后一个有效元素的下标 + 1
		- timeout : 阻塞时长
			0 : 不阻塞
			-1 : 阻塞，当检测到需要检测的文件描述符有变化，解除阻塞
			>0 : 阻塞的时长
	- 返回值：
		-1 : 失败
		>0（n） : 成功，n表示检测到集合中有n个文件描述符发生变化

```

<br>

short类型，表示发生事件

![image-20230215224613996](第四章Linux网络编程/image-20230215224613996.png)

<br>

**服务端**

- 在到客户端连接时，在accept之后要在pollfd数组中找一个靠前的位置来记录；

- 其他的跟select类似。

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <poll.h>


int main() {

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    struct sockaddr_in saddr;
    saddr.sin_port = htons(9999);
    saddr.sin_family = AF_INET;
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    bind(lfd, (struct sockaddr *)&saddr, sizeof(saddr));

    // 监听
    listen(lfd, 8);

    // 初始化检测的文件描述符数组
    struct pollfd fds[1024];
    for(int i = 0; i < 1024; i++) {
        fds[i].fd = -1;
        fds[i].events = POLLIN;
    }
    fds[0].fd = lfd;
    int nfds = 0;

    while(1) {

        // 调用poll系统函数，让内核帮检测哪些文件描述符有数据
        int ret = poll(fds, nfds + 1, -1);
        if(ret == -1) {
            perror("poll");
            exit(-1);
        } else if(ret == 0) {
            continue;
        } else if(ret > 0) {
            // 说明检测到了有文件描述符的对应的缓冲区的数据发生了改变
            if(fds[0].revents & POLLIN) {
                // 表示有新的客户端连接进来了
                struct sockaddr_in cliaddr;
                int len = sizeof(cliaddr);
                int cfd = accept(lfd, (struct sockaddr *)&cliaddr, &len);

                // 将新的文件描述符加入到集合中
                for(int i = 1; i < 1024; i++) {
                    if(fds[i].fd == -1) {
                        fds[i].fd = cfd;
                        fds[i].events = POLLIN;
                        break;
                    }
                }

                // 更新最大的文件描述符的索引
                nfds = nfds > cfd ? nfds : cfd;
            }
            
			// 最大的文件描述符 一定 大于等于它在数组中的下标。
            for(int i = 1; i <= nfds; i++) {
                if(fds[i].revents & POLLIN) {
                    // 说明这个文件描述符对应的客户端发来了数据
                    char buf[1024] = {0};
                    int len = read(fds[i].fd, buf, sizeof(buf));
                    if(len == -1) {
                        perror("read");
                        exit(-1);
                    } else if(len == 0) {
                        printf("client closed...\n");
                        close(fds[i].fd);
                        fds[i].fd = -1;
                    } else if(len > 0) {
                        printf("read buf = %s\n", buf);
                        write(fds[i].fd, buf, strlen(buf) + 1);
                    }
                }
            }

        }

    }
    close(lfd);
    return 0;
}

```

<br>

<br>





<br>

<br>

<br>

### 4.4.4 epoll

![image-20230215225908336](第四章Linux网络编程/image-20230215225908336.png)

1. 不用从用户态拷贝数据到内核，直接在内核中进行实例化和数据处理
2. 采用了更高效的数据结构
   - 红黑树：存储所有fd，用来遍历检测哪些发生数据改变
   - 双向链表：存放发生数据改变的fd的就绪列表



<br>

<br>

<br>

```c
#include <sys/epoll.h>


// 创建一个新的epoll实例。在内核中创建了一个数据，这个数据中有两个比较重要的数据，一个是需要检测的文件描述符的信息（红黑树），还有一个是就绪列表，存放检测到数据发送改变的文件描述符信息（双向链表）。
int epoll_create(int size);
	- 参数：
		size : 目前没有意义了。随便写一个数，必须大于0
	- 返回值：
		-1 : 失败
		> 0 : 文件描述符，操作epoll实例的fd
         
            
            
struct epoll_event {
	uint32_t events; /* Epoll events */
	epoll_data_t data; /* User data variable */
};
常见的Epoll检测事件：
	- EPOLLIN
	- EPOLLOUT
	- EPOLLERR
	- EPOLLET  // 边沿触发
  	- ...
    	
typedef union epoll_data {	// 是一个union
	void *ptr;
	int fd;
	uint32_t u32;
	uint64_t u64;
} epoll_data_t;

    
    
// 对epoll实例进行管理：添加文件描述符信息，删除信息，修改信息
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
	- 参数：
		- epfd : epoll实例对应的文件描述符
		- op : 要进行什么操作
			EPOLL_CTL_ADD: 添加
			EPOLL_CTL_MOD: 修改
			EPOLL_CTL_DEL: 删除
		- fd : 要检测的文件描述符
		- event : 检测文件描述符什么事情
            
            
// 检测函数
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
	- 参数：
		- epfd : epoll实例对应的文件描述符
		- events : 传出参数，一个epoll_event数组，保存了发生了变化的文件描述符
		- maxevents : 第二个参数结构体数组的大小
		- timeout : 阻塞时间
			- 0 : 不阻塞
			- -1 : 阻塞，直到检测到fd数据发生变化，解除阻塞
			- > 0 : 阻塞的时长（毫秒）
	- 返回值：
		- 成功，返回发送变化的文件描述符的个数 > 0
		- 失败 -1

```

<br>

<br>

<br>

**服务端**

根据监听读还是写事件来编写对应的处理

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/epoll.h>

int main() {

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    struct sockaddr_in saddr;
    saddr.sin_port = htons(9999);
    saddr.sin_family = AF_INET;
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    bind(lfd, (struct sockaddr *)&saddr, sizeof(saddr));

    // 监听
    listen(lfd, 8);

    // 调用epoll_create()创建一个epoll实例
    int epfd = epoll_create(100);

    // 将监听的文件描述符相关的检测信息添加到epoll实例中
    struct epoll_event epev;
    epev.events = EPOLLIN;
    epev.data.fd = lfd;
    epoll_ctl(epfd, EPOLL_CTL_ADD, lfd, &epev);

    struct epoll_event epevs[1024];

    while(1) {

        int ret = epoll_wait(epfd, epevs, 1024, -1);
        if(ret == -1) {
            perror("epoll_wait");
            exit(-1);
        }

        printf("ret = %d\n", ret);

        for(int i = 0; i < ret; i++) {

            int curfd = epevs[i].data.fd;

            if(curfd == lfd) {
                // 监听的文件描述符有数据达到，有客户端连接
                struct sockaddr_in cliaddr;
                int len = sizeof(cliaddr);
                int cfd = accept(lfd, (struct sockaddr *)&cliaddr, &len);
				
                // 将客户端文件描述符相关的检测信息添加到epoll实例中
                epev.events = EPOLLIN;
                epev.data.fd = cfd;
                epoll_ctl(epfd, EPOLL_CTL_ADD, cfd, &epev);
            } else {
                if(epevs[i].events & EPOLLOUT) {
                    continue;
                }   
                // 有数据到达，需要通信
                char buf[1024] = {0};
                int len = read(curfd, buf, sizeof(buf));
                if(len == -1) {
                    perror("read");
                    exit(-1);
                } else if(len == 0) {
                    printf("client closed...\n");
                    epoll_ctl(epfd, EPOLL_CTL_DEL, curfd, NULL);
                    close(curfd);
                } else if(len > 0) {
                    printf("read buf = %s\n", buf);
                    write(curfd, buf, strlen(buf) + 1);
                }
            }
        }
    }

    close(lfd);
    close(epfd);
    return 0;
}

```

<br>

<br>

<br>

#### epoll 两种工作模式

<br>

- **LT** 模式（**水平触发**）
  - 假设委托内核检测读事件 -> 检测fd的读缓冲区
  - 读缓冲区有数据 -> epoll检测到了会给用户通知
    - 用户不读数据，数据会一直在缓冲区，epoll会一直通知
    - 用户只读了一部分数据，epoll会通知
    - 用户读完数据，epoll不通知

能否一次读完得看缓冲区大小和每次读取的数量

> LT（level - triggered）是缺省（即默认）的工作方式，并且同时支持 **block 和 no-block socket**。在这种做法中，**内核告诉你一个文件描述符是否就绪了，然后你可以对这个就绪的 fd 进行 IO 操作**。如果你不作任何操作，**内核还是会继续通知你的**。

<br>

- **ET** 模式（**边缘触发**）
  - 假设委托内核检测读事件 -> 检测fd的读缓冲区
  - 读缓冲区有数据 -> epoll 检测到了会给用户通知
    - 用户不读数据，数据一致在缓冲区中，epoll下次检测时不通知
    - 用户只读了一部分数据，epoll不通知
    - 用户读完数据，epoll不通知

> - ET（edge - triggered）是高速工作方式，**只支持 no-block socket**。
> - 在这种模式下，当描述符从未就绪变为就绪时，内核通过epoll告诉你。然后它会假设你知道文件描述符已经就绪，并且不会再为那个文件描述符发送更多的就绪通知，**直到你做了某些操作导致那个文件描述符不再为就绪状态了**。但是请注意，如果一直不对这个 fd 作 IO 操作（从而导致它再次变成未就绪），内核不会发送更多的通知（only once）。
>
> - ET 模式在很大程度上减少了 epoll 事件被重复触发的次数，因此效率要比 LT 模式高。
> - epoll工作在 ET 模式的时候，必须使用**非阻塞**套接口，**以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。**

<br>

<br>

**客户端**

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

int main() {

    // 创建socket
    int fd = socket(PF_INET, SOCK_STREAM, 0);
    if(fd == -1) {
        perror("socket");
        return -1;
    }

    struct sockaddr_in seraddr;
    inet_pton(AF_INET, "127.0.0.1", &seraddr.sin_addr.s_addr);
    seraddr.sin_family = AF_INET;
    seraddr.sin_port = htons(9999);

    // 连接服务器
    int ret = connect(fd, (struct sockaddr *)&seraddr, sizeof(seraddr));

    if(ret == -1){
        perror("connect");
        return -1;
    }

    int num = 0;
    while(1) {
        char sendBuf[1024] = {0};
        // sprintf(sendBuf, "send data %d", num++);
        fgets(sendBuf, sizeof(sendBuf), stdin);

        write(fd, sendBuf, strlen(sendBuf) + 1);

        // 接收
        int len = read(fd, sendBuf, sizeof(sendBuf));
        if(len == -1) {
            perror("read");
            return -1;
        }else if(len > 0) {
            printf("read buf = %s\n", sendBuf);
        } else {
            printf("服务器已经断开连接...\n");
            break;
        }
    }

    close(fd);

    return 0;
}


```

<br>

<br>

<br>

**LT 服务端**

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/epoll.h>

int main() {

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    struct sockaddr_in saddr;
    saddr.sin_port = htons(9999);
    saddr.sin_family = AF_INET;
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    bind(lfd, (struct sockaddr *)&saddr, sizeof(saddr));

    // 监听
    listen(lfd, 8);

    // 调用epoll_create()创建一个epoll实例
    int epfd = epoll_create(100);

    // 将监听的文件描述符相关的检测信息添加到epoll实例中
    struct epoll_event epev;
    epev.events = EPOLLIN;
    epev.data.fd = lfd;
    epoll_ctl(epfd, EPOLL_CTL_ADD, lfd, &epev);

    struct epoll_event epevs[1024];

    while(1) {

        int ret = epoll_wait(epfd, epevs, 1024, -1);
        if(ret == -1) {
            perror("epoll_wait");
            exit(-1);
        }

        printf("ret = %d\n", ret);

        for(int i = 0; i < ret; i++) {

            int curfd = epevs[i].data.fd;

            if(curfd == lfd) {
                // 监听的文件描述符有数据达到，有客户端连接
                struct sockaddr_in cliaddr;
                int len = sizeof(cliaddr);
                int cfd = accept(lfd, (struct sockaddr *)&cliaddr, &len);

                epev.events = EPOLLIN;
                epev.data.fd = cfd;
                epoll_ctl(epfd, EPOLL_CTL_ADD, cfd, &epev);
            } else {
                if(epevs[i].events & EPOLLOUT) {
                    continue;
                }   
                // 有数据到达，需要通信
                char buf[5] = {0};
                int len = read(curfd, buf, sizeof(buf));
                if(len == -1) {
                    perror("read");
                    exit(-1);
                } else if(len == 0) {
                    printf("client closed...\n");
                    epoll_ctl(epfd, EPOLL_CTL_DEL, curfd, NULL);
                    close(curfd);
                } else if(len > 0) {
                    printf("read buf = %s\n", buf);
                    write(curfd, buf, strlen(buf) + 1);
                }

            }

        }
    }

    close(lfd);
    close(epfd);
    return 0;
}

```



<br>

<br>

<br>

**ET 服务端**

- 用fctnl设置连接客户端的文件描述符为非阻塞
- 设置客户端fd的epoll_event为边缘触发模式
- 读数据的时候用循环来读完
- 数据读完之后，在非阻塞的条件下，再去read对应的fd会引发**EAGAIN**，需要用条件判断避开

```c
#include <stdio.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/epoll.h>
#include <fcntl.h>
#include <errno.h>

int main() {

    // 创建socket
    int lfd = socket(PF_INET, SOCK_STREAM, 0);
    struct sockaddr_in saddr;
    saddr.sin_port = htons(9999);
    saddr.sin_family = AF_INET;
    saddr.sin_addr.s_addr = INADDR_ANY;

    // 绑定
    bind(lfd, (struct sockaddr *)&saddr, sizeof(saddr));

    // 监听
    listen(lfd, 8);

    // 调用epoll_create()创建一个epoll实例
    int epfd = epoll_create(100);

    // 将监听的文件描述符相关的检测信息添加到epoll实例中
    struct epoll_event epev;
    epev.events = EPOLLIN;
    epev.data.fd = lfd;
    epoll_ctl(epfd, EPOLL_CTL_ADD, lfd, &epev);

    struct epoll_event epevs[1024];

    while(1) {

        int ret = epoll_wait(epfd, epevs, 1024, -1);
        if(ret == -1) {
            perror("epoll_wait");
            exit(-1);
        }

        printf("ret = %d\n", ret);

        for(int i = 0; i < ret; i++) {

            int curfd = epevs[i].data.fd;

            if(curfd == lfd) {
                // 监听的文件描述符有数据达到，有客户端连接
                struct sockaddr_in cliaddr;
                int len = sizeof(cliaddr);
                int cfd = accept(lf d, (struct sockaddr *)&cliaddr, &len);

                // 设置cfd属性非阻塞
                int flag = fcntl(cfd, F_GETFL);
                flag |= O_NONBLOCK;
                fcntl(cfd, F_SETFL, flag);

                epev.events = EPOLLIN | EPOLLET;    // 设置边沿触发
                epev.data.fd = cfd;
                epoll_ctl(epfd, EPOLL_CTL_ADD, cfd, &epev);
            } else {
                if(epevs[i].events & EPOLLOUT) {
                    continue;
                }  

                // 循环读取出所有数据
                char buf[5];
                int len = 0;
                while( (len = read(curfd, buf, sizeof(buf))) > 0) {
                    // 打印数据
                    // printf("recv data : %s\n", buf);
                    write(STDOUT_FILENO, buf, len);
                    write(curfd, buf, len);
                }
                if(len == 0) {
                    printf("client closed....");
                }else if(len == -1) {
                    if(errno == EAGAIN) {
                        printf("data over.....");
                    }else {
                        perror("read");
                        exit(-1);
                    }
                    
                }

            }

        }
    }

    close(lfd);
    close(epfd);
    return 0;
}

```

<br>

<br>

<br>

<br>

<br>

## 4.5 UDP通信

### 基本

![image-20230216011718092](第四章Linux网络编程/image-20230216011718092.png)

**服务端**

- 创建socket
- 绑定ip端口
- 通信
- 关闭socket

**客户端**

- 创建socket
- 通信
- 关闭socket

<br>

```c
#include <sys/types.h>
#include <sys/socket.h>
or
#include <arpa/inet.h>

ssize_t sendto(int sockfd, const void *buf, size_t len, int flags, const struct sockaddr *dest_addr, socklen_t addrlen);
	- 参数：
		- sockfd : 通信的fd
		- buf : 要发送的数据
		- len : 发送数据的长度
		- flags : 0
		- dest_addr : 传出参数，指定通信的另外一端的地址信息
		- addrlen : 地址的内存大小
           
            
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags, struct sockaddr *src_addr, socklen_t *addrlen);
	- 参数：
		- sockfd : 通信的fd
		- buf : 接收数据的数组
		- len : 数组的大小
    	- flags : 0
		- src_addr : 传入参数，用来保存另外一端的地址信息，不需要可以指定为NULL
		- addrlen : 地址的内存大小

```



<br>

**服务端**

bind 绑定后不需要监听 listen

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <arpa/inet.h>

int main() {

    // 1.创建一个通信的socket
    int fd = socket(PF_INET, SOCK_DGRAM, 0);
    
    if(fd == -1) {
        perror("socket");
        exit(-1);
    }   

    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(9999);
    addr.sin_addr.s_addr = INADDR_ANY;

    // 2.绑定
    int ret = bind(fd, (struct sockaddr *)&addr, sizeof(addr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

    // 3.通信
    while(1) {
        char recvbuf[128];
        char ipbuf[16];

        struct sockaddr_in cliaddr;
        int len = sizeof(cliaddr);

        // 接收数据
        int num = recvfrom(fd, recvbuf, sizeof(recvbuf), 0, (struct sockaddr *)&cliaddr, &len);

        printf("client IP : %s, Port : %d\n", 
            inet_ntop(AF_INET, &cliaddr.sin_addr.s_addr, ipbuf, sizeof(ipbuf)),
            ntohs(cliaddr.sin_port));

        printf("client say : %s\n", recvbuf);

        // 发送数据
        sendto(fd, recvbuf, strlen(recvbuf) + 1, 0, (struct sockaddr *)&cliaddr, sizeof(cliaddr));

    }

    close(fd);
    return 0;
}

```



<br>

**客户端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <arpa/inet.h>

int main() {

    // 1.创建一个通信的socket
    int fd = socket(PF_INET, SOCK_DGRAM, 0);
    
    if(fd == -1) {
        perror("socket");
        exit(-1);
    }   

    // 服务器的地址信息
    struct sockaddr_in saddr;
    saddr.sin_family = AF_INET;
    saddr.sin_port = htons(9999);
    inet_pton(AF_INET, "127.0.0.1", &saddr.sin_addr.s_addr);

    int num = 0;
    // 3.通信
    while(1) {

        // 发送数据
        char sendBuf[128];
        sprintf(sendBuf, "hello , i am client %d \n", num++);
        sendto(fd, sendBuf, strlen(sendBuf) + 1, 0, (struct sockaddr *)&saddr, sizeof(saddr));

        // 接收数据
        int num = recvfrom(fd, sendBuf, sizeof(sendBuf), 0, NULL, NULL);
        printf("server say : %s\n", sendBuf);

        sleep(1);
    }

    close(fd);
    return 0;
}

```



<br>

<br>

<br>

### 广播

向 **子网** 中多台计算机发送消息，并且子网中所有的计算机都可以接收到发送方发送的消息，每个广播消息都包含一个特殊的IP地址（**广播地址**），除了子网ip外的主机部分的二进制位都为1。

- 只能在**局域网**中使用。
- **客户端**需要绑定**服务器广播使用的端口**，才可以接收到广播消息。

<br>

**服务端**

- 用setsockopt设置sockfd的广播属性
- 然后使用广播地址作为sendto的地址

```c
    // 2.设置广播属性
    int op = 1;
    setsockopt(fd, SOL_SOCKET, SO_BROADCAST, &op, sizeof(op));
    
    // 3.创建一个广播的地址
    struct sockaddr_in cliaddr;
    cliaddr.sin_family = AF_INET;
    cliaddr.sin_port = htons(9999);
    inet_pton(AF_INET, "192.168.193.255", &cliaddr.sin_addr.s_addr);

```

<br>

<br>

**客户端**

- sockfd 需要用 bind 绑定 本地IP和广播的端口

```c
    // 2.客户端绑定本地的IP和端口
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(9999);
    addr.sin_addr.s_addr = INADDR_ANY;

    int ret = bind(fd, (struct sockaddr *)&addr, sizeof(addr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

```



<br>

<br>

<br>

<br>

### 组播(多播)

- 单播地址标识单个 IP 接口，广播地址标识某个子网的所有 IP 接口，多播地址标识一组 IP 接口。
- 单播和广播是寻址方案的两个极端（要么单个要么全部），多播则意在两者之间提供一种折中方案。
- 多播数据报只应该由对它感兴趣的接口接收，也就是说由**运行相应多播会话应用系统的主机上的接口**接收。
- 另外，**广播一般局限于局域网内**使用，而多播则既可以用于局域网，也可以跨广域网使用。
  - **组播**既可以用于**局域网**，也可以用于**广域网**
  - **客户端**需要加入**多播组**，才能接收到多播的数据

<br>

<br>

**组播地址**

IP 多播通信必须依赖于 IP 多播地址，在 IPv4 中它的范围从 **224.0.0.0 到 239.255.255.255** ，并被划分为 **局部链接多播地址**、 **预留多播地址** 和 **管理权限多播地址** 三类:

|          IP地址           |                             说明                             |
| :-----------------------: | :----------------------------------------------------------: |
|   224.0.0.0~224.0.0.255   | 局部链接多播地址：为路由协议和其他用途保留的地址，路由器并不转发属于此范围的IP包 |
|   224.0.1.0~224.0.1.255   |  预留多播地址：公用组播地址，可用于Internet；使用前需要申请  |
| 224.0.2.0~238.255.255.255 |     预留多播地址：用户可用组播地址（临时），全网范围有效     |
| 239.0.0.0~239.255.255.255 | 本地管理组播地址，可供组织内部使用，类似于私有IP地址，不能用于Internet，可限制多播范围 |

<br>

<br>

**设置**

```c
int setsockopt(int sockfd, int level, int optname,const void *optval, socklen_t optlen);
	// 服务器设置多播的信息，外出接口
	- level : IPPROTO_IP
	- optname : IP_MULTICAST_IF
	- optval : struct in_addr
	// 客户端加入到多播组：
	- level : IPPROTO_IP
	- optname : IP_ADD_MEMBERSHIP
	- optval : struct ip_mreq

        
struct ip_mreq {
	/* IP multicast address of group. */
	struct in_addr imr_multiaddr; // 组播的IP地址
	/* Local IP address of interface. */
	struct in_addr imr_interface; // 本地的IP地址
};


struct in_addr {
	in_addr_t s_addr;
};

typedef uint32_t in_addr_t;

```

<br>

<br>

**服务端**

- setsockopt 设置 IPPROTO_IP 级别的 IP_MULTICAST_IF，传入组播的外出接口
- 将外出接口作为客户端地址信息，作为 sendto 指定的地址

```c

    // 2.设置多播的属性，设置外出接口
    struct in_addr imr_multiaddr;
    // 初始化多播地址
    inet_pton(AF_INET, "239.0.0.10", &imr_multiaddr.s_addr);
    setsockopt(fd, IPPROTO_IP, IP_MULTICAST_IF, &imr_multiaddr, sizeof(imr_multiaddr));
    
    // 3.初始化客户端的地址信息
    struct sockaddr_in cliaddr;
    cliaddr.sin_family = AF_INET;
    cliaddr.sin_port = htons(9999);
    inet_pton(AF_INET, "239.0.0.10", &cliaddr.sin_addr.s_addr);

    // 通信


```



<br>

**客户端**

- bind 绑定端口
- 指定ip_mreq结构体中的本地地址和组播地址
- setsockopt设置 IPPROTO_IP 级别的 IP_ADD_MEMBERSHIP ，传入上面（指定地址）的结构体

```c

    // 2.客户端绑定本地的IP和端口
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(9999);
    addr.sin_addr.s_addr = INADDR_ANY;

    int ret = bind(fd, (struct sockaddr *)&addr, sizeof(addr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

	// 加入到多播组
    struct ip_mreq op;
    inet_pton(AF_INET, "239.0.0.10", &op.imr_multiaddr.s_addr);
    op.imr_interface.s_addr = INADDR_ANY;

    setsockopt(fd, IPPROTO_IP, IP_ADD_MEMBERSHIP, &op, sizeof(op));

    // 3.通信


```

<br>

<br>

<br>

<br>

<br><br>

## 4.6 本地套接字通信

本地套接字的作用：**本地的进程间通信**

（本地套接字实现流程和网络套接字类似，一般采用 **TCP 的通信流程**）

- **有关系**的进程间的通信
- **没有关系**的进程间的通信

<br>

<br>

**struct sockaddr_un**

<img src="第四章Linux网络编程/image-20230216033138989.png" alt="image-20230216033138989" style="zoom:67%;" />

### 通信流程

本地套接字通信的流程 - tcp

<img src="第四章Linux网络编程/image-20230216033448663.png" alt="image-20230216033448663" style="zoom:80%;" />

**服务器端**

1. 创建监听的套接字

  ```c 
  int lfd = socket(AF_UNIX/AF_LOCAL, SOCK_STREAM, 0);
  ```

2. 监听的套接字 绑定 **本地的套接字文件** -> server端

  ```c
  struct sockaddr_un addr;
  // 绑定成功之后，指定的sun_path中的套接字文件会自动生成。
  
  bind(lfd, addr, len);
  ```

3. 监听

  ```c
  listen(lfd, 100);
  ```

4. 等待并接受连接请求

  ```c 
  struct sockaddr_un cliaddr;
  int cfd = accept(lfd, &cliaddr, len);
  ```

5. 通信
    接收数据：read/recv
    发送数据：write/send

6. 关闭连接
close();

<br>

**客户端**

1. 创建通信的套接字

  ```c 
  int fd = socket(AF_UNIX/AF_LOCAL, SOCK_STREAM, 0);
  ```

2. 监听的套接字绑定本地的IP,端口

  ```c 
  struct sockaddr_un addr;
  // 绑定成功之后，指定的sun_path中的套接字文件会自动生成。
  
  bind(lfd, addr, len);
  ```

3. 连接服务器

  ```c
  struct sockaddr_un serveraddr;
  
  connect(fd, &serveraddr, sizeof(serveraddr));
  ```

4. 通信
    接收数据：read/recv
    发送数据：write/send

5. 关闭连接
close();

<br>

<br>

```c
// 头文件: sys/un.h
#define UNIX_PATH_MAX 108
struct sockaddr_un {
	sa_family_t sun_family; // 地址族协议 af_local
	char sun_path[UNIX_PATH_MAX]; // 套接字文件的路径, 这是一个伪文件, 大小永远=0
};
```

<br>

<br>

### 案例

注意使用 **strcpy** 来设置 sockaddr_un 结构体中的 **sun_path** 的内容

**服务端**

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/un.h>

int main() {

    unlink("server.sock");

    // 1.创建监听的套接字
    int lfd = socket(AF_LOCAL, SOCK_STREAM, 0);
    if(lfd == -1) {
        perror("socket");
        exit(-1);
    }

    // 2.绑定本地套接字文件
    struct sockaddr_un addr;
    addr.sun_family = AF_LOCAL;
    strcpy(addr.sun_path, "server.sock");
    int ret = bind(lfd, (struct sockaddr *)&addr, sizeof(addr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

    // 3.监听
    ret = listen(lfd, 100);
    if(ret == -1) {
        perror("listen");
        exit(-1);
    }

    // 4.等待客户端连接
    struct sockaddr_un cliaddr;
    int len = sizeof(cliaddr);
    int cfd = accept(lfd, (struct sockaddr *)&cliaddr, &len);
    if(cfd == -1) {
        perror("accept");
        exit(-1);
    }

    printf("client socket filename: %s\n", cliaddr.sun_path);

    // 5.通信
    while(1) {

        char buf[128];
        int len = recv(cfd, buf, sizeof(buf), 0);

        if(len == -1) {
            perror("recv");
            exit(-1);
        } else if(len == 0) {
            printf("client closed....\n");
            break;
        } else if(len > 0) {
            printf("client say : %s\n", buf);
            send(cfd, buf, len, 0);
        }

    }

    close(cfd);
    close(lfd);

    return 0;
}

```



<br>

<br>

**客户端**

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/un.h>

int main() {

    unlink("client.sock");	
    // 删除参数pathname 指定的文件. 
    // 如果该文件名为最后连接点, 但有其他进程打开了此文件, 则在所有关于此文件的文件描述词皆关闭后才会删除. 
    // 如果参数pathname 为一符号连接, 则此连接会被删除。

    // 1.创建套接字
    int cfd = socket(AF_LOCAL, SOCK_STREAM, 0);
    if(cfd == -1) {
        perror("socket");
        exit(-1);
    }

    // 2.绑定本地套接字文件
    struct sockaddr_un addr;
    addr.sun_family = AF_LOCAL;
    strcpy(addr.sun_path, "client.sock");
    int ret = bind(cfd, (struct sockaddr *)&addr, sizeof(addr));
    if(ret == -1) {
        perror("bind");
        exit(-1);
    }

    // 3.连接服务器
    struct sockaddr_un seraddr;
    seraddr.sun_family = AF_LOCAL;
    strcpy(seraddr.sun_path, "server.sock");
    ret = connect(cfd, (struct sockaddr *)&seraddr, sizeof(seraddr));
    if(ret == -1) {
        perror("connect");
        exit(-1);
    }

    // 4.通信
    int num = 0;
    while(1) {

        // 发送数据
        char buf[128];
        sprintf(buf, "hello, i am client %d\n", num++);
        send(cfd, buf, strlen(buf) + 1, 0);
        printf("client say : %s\n", buf);

        // 接收数据
        int len = recv(cfd, buf, sizeof(buf), 0);

        if(len == -1) {
            perror("recv");
            exit(-1);
        } else if(len == 0) {
            printf("server closed....\n");
            break;
        } else if(len > 0) {
            printf("server say : %s\n", buf);
        }

        sleep(1);

    }

    close(cfd);
    return 0;
}

```



<br>

<br>

