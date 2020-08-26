**一、背景**

智能家居行业经过了十几年的发展，就现在而言，还称得上是方兴未艾。在互联网巨头的介入下后得到快速提升，智能家居概念在市场中得到广泛普及，而多项辅助技术也借此机会发展起来。

在许多行业都将目光投向智能化产业转型的时候，不能忽视的是，相关的技术必定是取得一定突破，才使智能化成为可能。

物联网，指利用各种信息传感设备，如射频识别装置、红外传感器、光扫描等种种装置与互联网结合起来而形成的一个巨大网络。它可以利用信息传感设备将家居生活有关的各种子系统有机地结合在一起，并与互联网连接起来，进行监控、管理信息交换和通讯，实现家居智能化。

**一个最常见的物联网场景**：
远在家里的硬件，要发一个信息给我的手机。 实现方案是，硬件和手机连接同一个MQTT 代理服务器(mqtt broker)，手机根据MQTT协议，注册一个话题A，硬件根据MQTT协议，向代理服务器发送话题A，附上信息。代理服务器，会转发给手机，至此完成物联网通信,硬件就是NodeMCU。
![图1、png](https://upload-images.jianshu.io/upload_images/4662103-65113453dcd15a83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



本大作业是基于MQTT协议的手机客户端控制NodeMcu模块的一个小钢琴的实现。

基本上能够实现控制，通讯功能，由于其他同学做出的各种监测温湿度，灯泡控制的比较多，所以我在上学期大作业的基础上进行了衍生，做到了通过APP中的**钢琴键**来控制**蜂鸣器**发出不同的音符，而产生美妙的音乐，看起来也很酷炫。

**二、系统设计**

APP界面：

![图2、APP界面设计.jpg](https://upload-images.jianshu.io/upload_images/4662103-1a971b465e35d031.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

主要功能：

手机APP上点击钢琴键1（do），2（re），3（mi），4（fa），5（sol），6（la），7（si），**NodeMcu**则控制**蜂鸣器**发出相应的音调，并且**灯泡**会在钢琴键按下的时候**闪烁**一下，可以根据给出的《两只老虎》的简谱来弹奏。

界面上下方已有曲目中，**点击**《葫芦娃》或者《天空之城》的按钮，**蜂鸣器**会发出一段完整的

曲子，并且在**Oled显示屏**上显示这首曲子的**英文名**。

**1、选择的协议：MQTT**

Android端实现消息推送的协议有很多种，而MQTT是一个轻量级的消息发布/订阅协议，它是实现基于手机客户端的消息推送服务器的理想解决方案。

MQTT的优点：

客户机较小并且 MQTT 协议 高效地使用网络带宽，在这个意义上，其为轻量级。MQTT 协议支持可靠的传送和即发即弃的传输。 在此协议中，消息传送与应用程序脱离。 脱离应用程序的程度取决于写入 MQTT 客户机和 MQTT 服务器的方式。脱离式传送能够将应用程序从任何服务器连接和等待消息中解脱出来。 交互模式与电子邮件相似，但在应用程序编程方面进行了优化。

**协议具有许多不同的功能：**

*   它是一种发布/预订协议。
*   除提供一对多消息分发外，发布/预订也脱离了应用程序。对于具有多个客户机的应用程序来说，这些功能非常有用。
*   它与消息内容没有任何关系。
*   它通过 TCP/IP 运行，TCP/IP 可以提供基本网络连接。
*   它针对消息传送提供三种服务质量：

*   **“****至多一次”**  消息根据底层因特网协议网络尽最大努力进行传递。 可能会丢失消息。  例如，将此服务质量与通信环境传感器数据一起使用。 对于是否丢失个别读取或是否稍后立即发布新的读取并不重要。
*   **“****至少一次”**  保证消息抵达，但可能会出现重复。
*   **“****刚好一次”**  确保只收到一次消息。  例如，将此服务质量与记帐系统一起使用。 重复或丢失消息可能会导致不便或收取错误费用。

*   它是一种管理网络中消息流的经济方式。 例如，固定长度的标题仅 2 个字节长度，并且协议交换可最大程度地减少网络流量。
*   它具有一种“遗嘱”功能，该功能通知订户客户机从 MQTT 服务器异常断开连接。请参阅“[最后的消息](http://www.ibm.com/support/knowledgecenter/zh/SS9D84_1.0.0/com.ibm.mm.tc.doc/tc60360_.htm)”发布。

因此，我们使用**MQTT**作为本次大作业通信协议，基本实现过程在后面的部分会详细介绍。

MQTT服务器的搭建在后面部分详细介绍。

**2、硬件单元**

硬件单元由NodeMcu，蜂鸣器，Oled显示屏，灯泡组成。

**NodeMcu**为主控设备，其他模块接在**NodeMcu**上，由NodeMcu来控制其他模块。

各个模块的控制方法在后面的部分有详细介绍。

**3、软件单元**

可自动连上MQTT服务器的APP。

APP连接服务器的方法在后面部分介绍。

**4、整个框图：**

![图3、整体系统实现的框图](https://upload-images.jianshu.io/upload_images/4662103-efd78875762185d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


由上图可知手机APP和NodeMcu通过MQTT服务器实现了相互通信。

**实现方式：**

硬件和手机连接同一个MQTT 代理服务器(mqtt broker)，手机根据MQTT协议，注册一个**话题A**，硬件根据MQTT协议，向代理服务器发送**话题A**，附上信息，代理服务器，会转发给手机。相反，硬件部分可以订阅一个**话题B**，手机向代理服务器发送信息到**话题B**，代理服务器，会转发给硬件，至此完成物联网通信,硬件就是NodeMCU。

**蜂鸣器等部分：**

 由NodeMcu控制，在不同的消息下产生不同的反应，比如发出不同音符，显示曲子名等。

**三、硬件设计**

1、 主要模块介绍：

**1)** **OLED显示屏**

![显示屏.jpg](https://upload-images.jianshu.io/upload_images/4662103-5c0812bc69eede7c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/4662103-8fd5196a8bf3b962.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




使用此模块需要安装以下的库：
![](https://upload-images.jianshu.io/upload_images/4662103-b8212dcaab858b38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装方法：在Arduino IDE中添加，项目->加载库->管理库->搜索上面两个库，并下载：

![](https://upload-images.jianshu.io/upload_images/4662103-c6762523a8f23528.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


首先调用前面下载的库文件：

![](https://upload-images.jianshu.io/upload_images/4662103-7049b06f0a488f1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


再定义接口：

![](https://upload-images.jianshu.io/upload_images/4662103-1ffdc99f43d65559.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


Void Setup（）中初始化：

![](https://upload-images.jianshu.io/upload_images/4662103-701dfaabf359d365.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


实际使用的方法，此部分显示了当前播放的歌曲的名字：

![](https://upload-images.jianshu.io/upload_images/4662103-8d24967ceac8b60e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**2)** **蜂鸣器模块**

![](https://upload-images.jianshu.io/upload_images/4662103-51592d6b1208ed9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


将蜂鸣器VCC和GND口直接接入正负极，I/O信号口接入NodeMCU的D4接口

让蜂鸣器响起来，以下的函数pin表示引脚，frequency表示频率

![](https://upload-images.jianshu.io/upload_images/4662103-baa2af63200dc410.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


停止发声：

![](https://upload-images.jianshu.io/upload_images/4662103-e034957168b518fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当然不同的乐音有着不同的频率，下面贴出对应的乐音频率表：

![](https://upload-images.jianshu.io/upload_images/4662103-faf2e27b4059ae52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/4662103-4083c6ecbfff3bfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/4662103-9de1bdb486c346bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实现音乐有两个基本的要素，第一是音符，上面已经给出；第二是节奏，也就是拍子，或者说音符时值。那么delay()函数可以用于延长这个音，因此我们如果规定四分音符的时延，编写一个时延数组就可以控制延长的时间。这些在代码中都运用到了。

**3)** **NodeMCU**

![NodeMcu模块](https://upload-images.jianshu.io/upload_images/4662103-3e88247df8f52ee8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![NodeMcu模块的引脚部分](https://upload-images.jianshu.io/upload_images/4662103-d140e715fad47615.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**NodeMcu**是一款开源快速硬件原型平台，包括固件和开发板，用几行简单的Lua脚本就能开发物联网应用。

**特点**：开源，交互式，可编程，低成本，简单，智能，WI-FI硬件。

![](https://upload-images.jianshu.io/upload_images/4662103-6e3bb7b067733562.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


基于**乐鑫esp8266**的NodeMcu开发板，具有GPIO、PWM、I2C、1-Wire、ADC等功能，结合NodeMcu 固件为您的原型开发提供最快速的途径。

**使用方法：**

首先在Arduino IDE中安装固件，这样可以直接用c++来编程：

![](https://upload-images.jianshu.io/upload_images/4662103-ccf94933e624847b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


将NodeMcu用usb数据线接在电脑上，并且在“工具“中选用NodeMcu开发板，这样就可以进行开发。

![](https://upload-images.jianshu.io/upload_images/4662103-5759402eb77e9ded.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**2、整个系统介绍**

1、电路图：

![](https://upload-images.jianshu.io/upload_images/4662103-547ce8bcbc1c143a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2、模块之间的联系：

将硬件代码烧录在NodeMcu板上，NodeMcu利用WiFi功能连上手机热点或者无线网，再通过MQTT协议的功能来订阅某一个特定的topic，从而做到能从其他设备中（本项目中是我的手机）受到消息，根据收到的消息的不同，从而来控制蜂鸣器发出的声音的音调不同，灯泡的亮灭和显示屏是否显示数据。

3、实物图：

![](https://upload-images.jianshu.io/upload_images/4662103-5f90cfb1ee1bd4cf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**四、软件设计**

**1、** **主要通讯协议之MQTT**

**1)MQTT**
**服务器搭建**

.在http://activemq.apache.org/apollo/download.html下载Apollo服务器，解压后安装。
.命令行进入安装目录bin目录下（例：E:>cd E:\MQTT\apache-apollo-1.7.1\bin）。
.输入apollo create XXX（xxx为创建的服务器实例名称，例：apollo create mybroker），之后会在bin目录下创建名称为XXX的文件夹。XXX文件夹下etc\apollo.xml文件下是配置服务器信息的文件。etc\users.properties文件包含连接MQTT服务器时用到的用户名和密码，默认为admin=password，即账号为admin，密码为password，可自行更改。
.进入XXX/bin目录，输入apollo-broker.cmd run开启服务器，看到如下界面代表搭建完成

![](https://upload-images.jianshu.io/upload_images/4662103-28e7855aa95ae6a9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

之后在浏览器输入[http://127.0.0.1:61680/](http://127.0.0.1:61680/)，查看是否安装成功
![](https://upload-images.jianshu.io/upload_images/4662103-c75427ad5dfbf5e4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

输入：username：admin； password：password进入下面的界面：

![](https://upload-images.jianshu.io/upload_images/4662103-7a21ce0e72119e29.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**2）MQTT安卓客户端的具体实现**

基本概念：

![](https://upload-images.jianshu.io/upload_images/4662103-70fb82e1734333c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


导入jar包到安卓目录下的libs：

![](https://upload-images.jianshu.io/upload_images/4662103-66aeeed5e4715c34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


给软件添加权限：

![](https://upload-images.jianshu.io/upload_images/4662103-872529e8169ecd4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


连接设置（IP为本机的IP地址，在cmd中ipconfig/all中获得）：

![](https://upload-images.jianshu.io/upload_images/4662103-25af1288d35247f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


对于安卓连接MQTT服务器的方法以及代码在以下网址中有详细介绍：

[http://blog.csdn.net/qq_17250009/article/details/52774472](http://blog.csdn.net/qq_17250009/article/details/52774472)

一旦连接上服务器后，安卓端就可以向服务器中的topic发送消息了

下图为发送消息到topic-“lxr“的代码，修改msg的内容即可实现发送不同的控制信息

![](https://upload-images.jianshu.io/upload_images/4662103-920e542adfcd6cd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


举一个例子：在布局文件中有1（do）按钮，设置点击事件，当按下1时，将msg=“a“，再发送到topic-“lxr”，那么所以订阅了 “lxr”的设备均可以接受到“a”,从而产生不同的音调。

安卓中代码（D1为按钮1的id）：



Arduino端的代码：

**连上MQTT的时候订阅 topic “lxr”：**

![](https://upload-images.jianshu.io/upload_images/4662103-a71798d0e82ad2f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Void loop()****中调用****callback****：**

![](https://upload-images.jianshu.io/upload_images/4662103-be77c5465b7103c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**之后会不停的调用下图的函数，对收到的信息做出判断：**

![](https://upload-images.jianshu.io/upload_images/4662103-df7db80db5409f2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**2、主要功能之蜂鸣器播放天空之城（葫芦娃）**

如下图，按照本报告中的硬件设计中的音调部分把每个需要的音符和频率值对应起来：

![](https://upload-images.jianshu.io/upload_images/4662103-33f652784ddc40eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


节拍部分：

![](https://upload-images.jianshu.io/upload_images/4662103-96dde110773f869a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这里定义一个变量，后面用来表示共有多少个音符

![](https://upload-images.jianshu.io/upload_images/4662103-7ead3c2b79314543.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



整首曲子的音符的排列顺序单独放在一个数组内：

![](https://upload-images.jianshu.io/upload_images/4662103-ba8e53742d08dcf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


整首曲子的节拍部分排列顺序：

![](https://upload-images.jianshu.io/upload_images/4662103-57726c77cb9ddb52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


主体循环部分的代码：

![](https://upload-images.jianshu.io/upload_images/4662103-600365abc235a381.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


将上述的代码封装在一个函数中，在callback函数中调用即可。

由天空之城这首曲子的例子可知，我们能用这种方法写出更多的曲子的音符节拍的排列顺序，这样就可以把他们播放出来，葫芦娃就是用了同样的方法，其他的歌曲也都可以。

**五、使用说明**                                                             

1、将NodeMcu通上电源，连好电路，确保无接触不良，然后运行代码，连上WiFi以及MQTT。

2、将安卓代码运行，并且下装到自己手机上，会出现如下图所示的界面：

![](https://upload-images.jianshu.io/upload_images/4662103-68865d817f0b905e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


3、APP使用方法：

可弹曲目表示：  1（do），2（re），3（mi），4（fa），5（sol），6（la），7（si）键分别代表着不同的音调，根据所示的简谱点击按键会演奏出两只老虎的音乐。对照着网上很多其他的曲谱也可以弹出一些比较简单的音乐。

已有曲目：有天空之城和葫芦娃，这些是我事先写在代码的音乐，tone函数中调用了大概20多种频率，不只是上面图中所示的7种。这两首歌点击按钮，蜂鸣器即可演奏出美妙的音乐。




