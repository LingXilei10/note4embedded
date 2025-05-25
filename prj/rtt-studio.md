# 1. 新建基于芯片的工程

​	芯片选择STM32F103ZET，控制台串口选择UART1（PA9/10），使用开发板上的调试串口，调试器选择ST-Link，接口是SWD。

# 2. 修改drv_clk.c

​	选择使用HSE作用PLL时钟源，配置成72MHz的CPU频率。

# 3. 配置AT组件和MQTT软件包

## 3.1 添加MQTT软件包

​	这里添加的是Paho MQTT，鼠标放在MQTT软件包上进入配置项。使能示例，改变订阅主题的最大个数，根据需求改，假设我们改为2，如图所示：

<img src="E:\076lxl\work\note4embedded\prj\assets\1_配置paho mqtt.jpg" style="zoom:90%;" />

## 3.2 使能AT客户端应用

​	我们在之前的课程已经知道，F103是使用的ESP8266连接服务器，用的是AT命令，所以我们需要使能AT客户端组件，如图所示：

![](E:\076lxl\work\note4embedded\prj\assets\2_使能AT客户端.jpg)

点击“应用文档”可以学习了解如何使用这个组件，我们还可以点击配置项进去配置细节：

![](E:\076lxl\work\note4embedded\prj\assets\3_AT配置细节.jpg)

从应用文档中我们可以清楚的给我们这个开发板设备定位，是一个AT客户端，因为是开发板向服务器发送指令得到反馈进行控制的，所以只需要使能AT命令客户端即可，又我们只连接了一个ESP8266所以只用了一个客户端，数量设置为1，其它的可以不用动。

## 3.3 添加AT设备

​	我们是怎么知道要添加AT设备的呢？是从AT客户端的应用文档中的【常见问题】中了解到的：

![](E:\076lxl\work\note4embedded\prj\assets\4_AT设备.jpg)

这个配置在AT客户端的应用文档中，正文好像都没有提到，也是踩了坑之后发现的，现在将这个结论高速大家，让大家少走一点弯路。

​	我们可以在软件包的详细配置中搜索到AT设备，如图所示：

![](E:\076lxl\work\note4embedded\prj\assets\5_搜索AT设备.jpg)

我们将其使能，选择具体的设备，我们使用的是ESP8266，所以选择ESP8266，去里面配置连接用的串口、连接的热点名和密码以及缓存长度，如图所示：

![](E:\076lxl\work\note4embedded\prj\assets\6_配置ESP8266.jpg)

我们使能了socket和示例，目的是方便参考使用，减少我们的应用开发难度，提高效率。根据扩展板原理图知道我们开发板和ESP8266是通过USART3连接的，在rtt里面名称叫做uart3。

## 3.4 其它依赖

​	使用了AT组件和MQTT软件包还会用到其它依赖，但是RT-Thread Studio的厉害之处就在于可以自动关联，不需要我们一个一个的去找去使能。

## 3.5 保存配置并且初步构建工程

​	配置好AT组件、AT设备和MQTT软件包之后，就按CTRL+S保存配置生效到工程，然后构建工程（快捷键CTRL+B），这样配置后构建应该是不会出错的，但是第3不的工作还没有完成，从何得知呢？将程序烧写到开发板上看效果。烧写程序在studio中点击按钮：

![](E:\076lxl\work\note4embedded\prj\assets\7_烧写按钮.jpg)

可以点击红框里的下拉键设置调试下载器，我们将程序烧进去看下效果：

![](E:\076lxl\work\note4embedded\prj\assets\8_AT配置效果.jpg)

它提示我们什么呀？说找不到uart3设备！这个应该怎么弄呢？这也是经验之谈了哈，直接高速大家结论：去board.h中宏定义UART3的配置，参考shell命令段的UART1配置。

​	在board.h里面找到UART的配置处：

![](E:\076lxl\work\note4embedded\prj\assets\9_UART1的BSP配置.jpg)

添加我们用到的UART3的BSP配置：

```C
#define BSP_USING_UART3
#define BSP_UART3_TX_PIN       "PD8"
#define BSP_UART3_RX_PIN       "PD9"
```

根据原理图知道USART3的TX和RX引脚分别是PD8和PD9，配置如图所示：

![](E:\076lxl\work\note4embedded\prj\assets\10_添加UART3配置.jpg)

然后保存重新编译下载观察效果：

![](E:\076lxl\work\note4embedded\prj\assets\11_AT成功配置效果.jpg)

main线程一直打印干扰我们调试，我们先将其注释掉：

![](E:\076lxl\work\note4embedded\prj\assets\12_注释main中的调试打印.jpg)

保存编译重新下载，然后再串口助手命令行输入`mqtt：+键盘TAB键

![](E:\076lxl\work\note4embedded\prj\assets\13_查询mqtt.jpg)

注意：图中红色错误是因为我按了复位键，ESP8266的复位信息长度超过了512bytes大小，所以报了这个错误，不影响后续使用。

我们输入mqtt查询得到图中的指令，我们使用`mqtt_start`试试：

![](E:\076lxl\work\note4embedded\prj\assets\14_初试mqtt_start.jpg)

可以看到mqtt连接的服务器地址是默认的，无法连接上，所以就需要去适配MQTT。

## 3.6 适配MQTT

​	我们去MQTT的示例中适配体验下，我们需要改变的是mqtt_sample.c里面的信息：

![](E:\076lxl\work\note4embedded\prj\assets\15_mqtt_sample初始数据.jpg)

我们以阿里云物联网平台为例：

![](E:\076lxl\work\note4embedded\prj\assets\16_阿里云物联网设备参数.jpg)

可以看到sample里面的URL、USERNAME、PASSWORD都需要修改，还要修改一个ClientID，是在sample里的：

![](E:\076lxl\work\note4embedded\prj\assets\17_sample里的cid.jpg)

我们也将其用宏定义，所以服务器地址和阿里云物联网设备三元组参数在sample里面的宏定义如下：

```C
#define MQTT_URI                "tcp://iot-06z00b8655ibl9u.mqtt.iothub.aliyuncs.com:1883"
#define MQTT_CLIENTID			"gvs7ys2aOMH.F103_Pro|securemode=2,signmethod=hmacsha256,timestamp=1655221024772|"
#define MQTT_USERNAME           "F103_Pro&gvs7ys2aOMH"
#define MQTT_PASSWORD           "563423a9142db8e256cc149fe0d2b51d2a02841c32037f748acaa02cc92c8551"
```

顺便去mqtt_start中将cid删掉使用宏定义的clientid：

![](E:\076lxl\work\note4embedded\prj\assets\19_删掉cid.jpg)

然后我们还看到有发布和订阅的主题，我们还是用阿里云物联网平台的产品支持的topic：

![](E:\076lxl\work\note4embedded\prj\assets\18_阿里云物联网平台的topic.jpg)

根据自己的需求以及topic的权限，可以自定义topic，也可以直接使用系统支持的，我们这里就选择订阅/user/get这个topic，以及发布/user/update这个主题，所以在sample里面topic的定义如下：

```C
#define MQTT_SUBTOPIC           "/gvs7ys2aOMH/F103_Pro/user/get"
#define MQTT_PUBTOPIC           "/gvs7ys2aOMH/F103_Pro/user/update"
```

然后保存编译，构建下载观察效果：

![](E:\076lxl\work\note4embedded\prj\assets\20_mqtt初始化成功.jpg)

也去阿里云上看一下设备是否上线：

![](E:\076lxl\work\note4embedded\prj\assets\21_阿里云设备在线.jpg)

设备上线了，这就表明mqtt初始化成功了，并且订阅主题也成功了，我们从阿里云服务器下发消息过去试试，点击设备，选择【Topic列表】，根据订阅的主题选一个发布消息：

![](E:\076lxl\work\note4embedded\prj\assets\22_设备选主题发布.jpg)

![](E:\076lxl\work\note4embedded\prj\assets\23_发布消息.jpg)

点击确认后，我们来串口助手观察：

![](E:\076lxl\work\note4embedded\prj\assets\24_硬件设备现象.jpg)

可以看到收到服务器的消息了。

我们再去看一下服务器是否收到了开发板上传的数据，去设备的【日志服务】：

![](E:\076lxl\work\note4embedded\prj\assets\25_设备日志服务.jpg)

然后再串口命令行输入指令发布数据：

![](E:\076lxl\work\note4embedded\prj\assets\26_开发板发布消息给云.jpg)

阿里云的日志反应：

![](E:\076lxl\work\note4embedded\prj\assets\27_阿里云日志反应.jpg)

点击那个【查看】来查阅消息：

![](E:\076lxl\work\note4embedded\prj\assets\28_日志消息.jpg)

至此，已经能够证明开发板向云上传消息和接收消息已经完全走通了。

# 4. 配置DHT11

​	我们再软件包中搜索DHT11并将其加入到工程：

![](E:\076lxl\work\note4embedded\prj\assets\29_添加DHT11软件包.jpg)

然后去配置：

![](E:\076lxl\work\note4embedded\prj\assets\30_配置DHT11细节.jpg)

这里没啥好配置的，主要还是在程序里面适配。我们现在不知道该如何改动程序适配，所以直接保存RT-Thread Settings生效到工程，然后构建工程看报错信息：

![](E:\076lxl\work\note4embedded\prj\assets\31_dht11工程构建报错.jpg)

提示找不到这个头文件，这是因为现版本的rtt将这个头文件整合到了“drv_common.h”里面去了，我们双击这个红色报错信息就能跳到错误的地方：

![](E:\076lxl\work\note4embedded\prj\assets\32_修改错误和引脚.jpg)

在这里不仅看到了头文件错误，还看到了定义DHT11数据线的引脚，漠然是PB12，但是我们的扩展板使用的是PF6，所以将代码改成下面这个样子“

![](E:\076lxl\work\note4embedded\prj\assets\33_改动后的dht11.jpg)

然后保存编译构建下载观察现象：

![](E:\076lxl\work\note4embedded\prj\assets\34_添加DHT11后开发板现象.jpg)

可以看到例程线程已经跑起来了，读到了温湿度。我们后面的工作就是将这个温度数据发给mqtt上传到云端。

注意：DHT11头两次读取数据为0应该是初始化后立即读取，给DHT11反应的时间短了导致的。

# 5. 温湿度数据上云

​	我们现在已经有两个设备，DHT11读取温湿度，是创建了一个线程轮询读取；MQTT则是需要根据指令控制连接和断开。我们现在期望将DHT11读取到的数据传递给MQTT，让MQTT上传给云，所以MQQ那边其实还缺少一个获取数据的线程，需要我们自己去实现。

​	另外，线程之间的通信也是需要我们实现的，我们这次是需要传递温湿度数据，温度和湿度各占1个字节，所以我们就可以设计为传递一个以uint16_t为单位的消息队列，DHT11写队列，MQTT读队列，读到了就上传，所以我们在程序中的实现是这样的：

```C
// MQTT
rt_mq_t pub_mqt = RT_NULL;
static void get_publish_data_entry(void *parameter)
{
    rt_uint8_t temp = 0, humi = 0;
    rt_uint16_t data = 0;

    at_client_t at_client = RT_NULL;
    while(at_client == RT_NULL) at_client = at_client_get_first();
    while(at_client_obj_wait_connect(at_client, 5000) != RT_EOK);

    mqtt_start(1, RT_NULL);

    while(1)
    {
        if(is_started!=1 && pub_mqt == RT_NULL)
        {
            rt_thread_delay(1);
            continue;
        }
        /* 从消息队列中接收消息 */
        if (rt_mq_recv(pub_mqt, &data, sizeof(data), RT_WAITING_FOREVER) == RT_EOK)
        {
            temp = (data>>8)&0xFF;
            humi = data&0xFF;
            char buf[64] = {0};
            rt_sprintf(buf, "temp:%d\thumi:%d", temp, humi);
            rt_kprintf("mqtt thread: msg frmo queue[%s]\r\n", buf);
            paho_mqtt_publish(&client, QOS1, MQTT_PUBTOPIC, buf);
        }
    }
}

int mqtt_thread_init(void)
{
    pub_mqt = rt_mq_create( "mqtt_pub_mqt", sizeof(rt_uint16_t), 64, RT_IPC_FLAG_FIFO);
    if(pub_mqt == RT_NULL)
    {
        rt_kprintf("init msg queue failed.\r\n");
        return -1;
    }

    rt_thread_t tid;
    tid = rt_thread_create("mqtt_thread",
                        get_publish_data_entry,
                        RT_NULL,
                        1024,
                        RT_THREAD_PRIORITY_MAX / 2,
                        20);
    if (tid != RT_NULL)
    {
        rt_thread_startup(tid);
    }

    return 0;
}
INIT_APP_EXPORT(mqtt_thread_init);


// DHT11
if (sensor_data.data.temp >= 0)
{
    uint8_t temp = (sensor_data.data.temp & 0xffff) >> 0;      // get temp
    uint8_t humi = (sensor_data.data.temp & 0xffff0000) >> 16; // get humi
    rt_uint16_t data = (temp<<8) | (humi);
    extern rt_mq_t pub_mqt;
    if(pub_mqt != RT_NULL)
    {
        rt_err_t ret = rt_mq_send(pub_mqt, (char*)&data, sizeof(data));
        if(ret != RT_EOK)
        {
            rt_kprintf("rt mq send failed\r\n");
        }
    }
}
```

另外DHT11的初始化在多线程中其实是有点问题的，因为它是单总线，使用的是rt的延时函数实现的时序，会被其它任务打断，导致时序混乱初始化失败，所以我们将初始化函数放到了线程中，让DHT11的线程和其它最高优先级的线程同等优先级，这样来解决这个问题。