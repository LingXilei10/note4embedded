tolist：

- [ ] 蓝牙扫码
- [ ] bms
- [ ] 手环
- [ ] linux电子阅读器、**相机（摄像头驱动？）**、视频播放器
- [ ] （迷宫小车？

2-26 

- [x] 汇编
- [ ] 线程调度
- [ ] 消息队列

2-27

- [ ] 邮箱
- [ ] 信号量
- [ ] 互斥量
- [ ] 事件组

2-28

- [ ] 引脚驱动
- [ ] 按键驱动
- [ ] LCD驱动
- [ ] I2C驱动
- [ ] SPI
- [ ] UART



1.链表看完

2.哈希表看个一大半

3.整理别人做的项目

但新项目 需要有rtos 或系统移植



智能车赛q：

摄像头如何获取图像

摄像头驱动

 

[基于FREERTOS的STM32多功能手表（软件设计）_freertos制作智能手表-CSDN博客](https://blog.csdn.net/wyhnbkls/article/details/139375000)



摄像头、音频处理

 FreeRTOS的IoT项目，涉及网络相关的（MQTT、CoAP，TCP、UDP，4G、NB、WIFI、Zigbee、LoRA，云平台的对接）

MQTT协议

LVGL 触摸屏  移植

sd卡

嵌入式系统与上位机实时通讯

-直播公开课：C语言提高篇 

-直播公开课：单片机开发过程中的两个调试绝招 

-嵌入式Linux快速入门 

-毕设级项目：使用MQTT实现智能家居（三合一） 

-【第6期】7天物联网智能家居实战训练营 

-韦东山·直播公开课专栏RTOS实战项目之实现多任务系统 -直播公开课：RTOS商业产品案例源码讲解 -FreeRTOS入门与工程实践-基于STM32 

-ARM架构与编程·基于STM32F103 

-百问网STM32瑞士军刀HAL快速入门与项目实战



路线

1. 个人认为只有51的基础还不能算把C语言练得比较熟练（除非你用51做过很牛的项目），也应该不太懂库函数和寄存器，所以建议先学STM32，最终能达到做出一个物联网相关的项目，这样可以保证你即对单片机开发很熟悉，也会对C语言的使用比较熟练，我认为这些都是必不可少的基础（大楼的地基） PS：项目中建议可以加APP、微信小程序这些，丰富自己知识的广度 
2.  RTOS如果感兴趣可以去学，建议FreeRTOS 或者 RT-Thread，其实现在回头看，当你做STM32项目时就可以先学一个RTOS，只学简单的任务创建，随着项目功能的增加，可以选择性的去用一些RTOS中的信号量、互斥锁、消息队列这些，这样项目做完，STM32和RTOS就算比较熟悉了，当然花费时间会再多一些。 
3. 在项目中也可以考虑加个上位机，比如QT，这时候你就有QT开发的经验了（看个人需求）
4. 做完STM32的项目就可以考虑学习嵌入式Linux了，网上大多数的学习流程可能是：Linux基础命令入门、ARM 裸机（也可能有汇编）、Linux内核移植、Linux根文件系统构建、uboot移植、然后才是Linux驱动开发、Linux应用开发 
5.  我个人推荐的路线是这样的： 了解ubuntu下Linux的基本命令（不多，够用就行） --> 找自己板子对应的教程，直接找到他烧录系统的那几节视频，通过现成的工具，和他们提供好的内核、文件系统等等烧录到板子上，让自己的板子先拥有学习驱动开发的Linux环境 --> 直接学驱动（其实在学习驱动期间我也了解了一些uboot，因为要从网络加载内核，这一部分我跳过了，后来自己从网上找的教程，跟着做了一下，所以其他几个跳过的部分还是有并存的机会可以学习的） --> 跳过LCD的触摸屏驱动（官网有的），直接找一个QT移植的教程，移植一下 ，就开始做Linux+QT的项目了 
6. 做完项目后可以回过头来，站在 “Linux+QT” 的肩膀上选择性的学uboot移植、内核移植/裁剪、文件系统制作了，我目前就处于这个阶段，发自肺腑的说，现在让我学上面提到的这三个，不会有反感或者想放弃的想法了，从后往前、自上而下的学习还是不错的选择



