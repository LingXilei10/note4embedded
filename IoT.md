**物联网中的通信技术有哪些？**

物联网的通信技术分为[有线通信技术](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=有线通信技术&zhida_source=entity)和[无线通信技术](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=无线通信技术&zhida_source=entity)。

**有线通信技术**

有线通信技术有**以太网、RS-232、RS-485、M-Bus、PLC**等，有线通信稳定性强，可靠性高。但是受限于媒介。

以太网：一种计算机局域网技术。现有局域网采用的最通用的通信协议标准，另外又细分为标准以太网、快速以太网、10G以太网。在智慧家庭物联网中，家庭路由器宽带技术涉及以太网技术。

两个通讯接口：RS-232（个人计算的接口之一，一般计算机都有两组。在监控和控制系统应用中得到普遍的应用）和RS-485（为了解决RS-232这种接口可以实现点对点通信但是不能联网的问题而发展起来的），区别在于**前一个采用不平衡传输方式，即单端传输，而后者实现平衡传输，即差分传输方式**。传输距离上前者只能有20米，后者可以有几十到上千米。**通信数量上，前者只能一对一，后者可以同时连接128个收发器**。

M-Bus：全称Meter Bus，远程抄表系统，是一种用户非电力户用仪表传输的欧洲总线标准，专门为消耗测量仪器和计数器传送信息的数据总线设计。满足了远程供电或电子供电要求，可以在几公里的线路上连接几百个设备。

PLC：电力线通信。基于电线传播，应用场景多种多样，除了抄表（工业网关），还可以应用于家庭PC端之间的传统数据处理设备、家庭安全方面。

**无线通信技术**

无线通信技术现在发展的比有线通信技术稍微广泛些，无线通信技术主要分为两大类有：[蜂窝移动通信技术](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=蜂窝移动通信技术&zhida_source=entity)（**2/3/4G**）、[短距离无线通信技术](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=短距离无线通信技术&zhida_source=entity)（**蓝牙、WIFI、ZigBee**、Zwave）

蜂窝移动通信：2/3/4/5G。目前的共享单车、POS机、ATM机等都是通过GPRS（2G）技术，因为2G技术成本较低，并且传输速率在以上场景已经满足基本需求。

短距离无线通信技术：蓝牙、WIFI、ZigBee、Z-WAVE等。

——蓝牙：最初是作为RS-232的替换方案而出现的。蓝牙的容量大，适合近距离传输大文件，传输距离为10厘米到10米，最高传输速率1MPS。蓝牙速率快、低功耗、并且安全性高，但是网络节点少，不适合多点布控。

——WIFI：允许电子设备连接到无线局域网（WLAN）的技术，通常使用2.4G UHF或 5G SHF ISM的射频频段。WIFI的覆盖范围广，数据传输速率快，但是传输安全性不好，稳定性差，并且功耗略高。

——ZigBee：ZigBee是短距无线通信技术中目前应用较多的一项技术，基于IEEE802.15.4标准的低功耗局域网协议，与蜂窝移动通信不同的是，它广泛应用于工业和智慧家庭领域，简单，使用方便，工作可靠，成本价值低。移动通信网主要是为语音通信而建立的，建议一个基站需要壹佰万元人民币。而建立一个ZigBee基站只需要一千元人民币。每个ZigBee网络节点不仅本身可以作为监控对象（例如，其网络节点所连接的传感器直接进行数据采集和监控），还可以自动中转别的网络节点传过来的数据资料。ZigBee适合较短距离传输，功耗低，数据速率要也较低。另外，虽然ZigBee在点对点传输距离达到几百米，但是适合在比较空旷的场景使用，不适合智能停车场景，在停车的场景中大型车辆会大大的衰弱信号。而且ZigBee对于不同芯片兼容性较差，网络较灵活，不易维护。

——Zwave：一种新兴的基于射频的、低成本、低功耗、高可靠的短距离无线通信技术。应用于智能家居、照明控制、智能抄表、防盗系统等应用，在室内可达到几十米，在室外可达到几百米。缺点是速率较低，芯片标准不开放。

![img](https://pic1.zhimg.com/v2-49afddc4fa5ca2a9618b45dea4381f60_1440w.jpg)

随着物联网的发展，后面有了算是专门针对物联网传输的万物互联的[LPWA技术](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=LPWA技术&zhida_source=entity)：LPWA（低功耗广域网）通信技术应运而生，是物联网产业发展的一个新机遇。他解决了传统网络不能应对一些物联网场景的通信问题，LPWA技术中有三种方式：[SigFox](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=SigFox&zhida_source=entity)、[LoRa](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=LoRa&zhida_source=entity)、[NB-IOT](https://zhida.zhihu.com/search?content_id=100494354&content_type=Article&match_order=1&q=NB-IOT&zhida_source=entity)。

——SigFox：无线物联网专用技术，使用UNB（超窄带）技术，低功耗，低成本，传输功耗水平非常低，却还能维持一个稳定的数据连接，通常传输速率只有100bps。

——**LoRa：基于开源的MAC层协议的低功耗广域网标准，超远距离无线传输方案，**长电池寿命，大容量。

——NB-IOT：构建于蜂窝通信的窄带物联网，只消耗大约180KHZ的带宽，可直接部署于GAM网络、UMTS网络、LTE网络以降低部署成本，实现平滑升级。聚焦于低功耗广域网市场，覆盖广、连接多、速率低、成本低、功耗低、价格优的特点，可用于远程抄表、资产追踪、只能停车、智慧农业等。

