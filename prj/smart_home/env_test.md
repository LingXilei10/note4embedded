# 手持环境测量仪

温湿度传感器



气压传感器

有害气体传感器

## 电量测量

电池的最大电压为4.2V，最小工作电压3.2V（要根据具体情况确定）。

> 最小工作电压要根据具体情况确定，可能3.4V就无法工作了

因为GD32的IO口最大可以兼容5V，超过5V就会把IO口烧坏；而**测量电压的外设ADC，它的参考电压在立创·梁山派开发板上是3.3V**。所以**如果使用ADC直接测量电池的电压，那么它无法测量3.3V以上的电压**。

电量测量的电路原理图如下。

<img src="E:\076lxl\work\note4embedded\pic\adc.png" alt="adc" style="zoom:50%;" />

为了解决这个问题，我们可以通过**电阻分压的形式测量电池电压**。电阻的大小可以根据IO口电平计算，分压后不要超过IO口容忍的电压的即可，选取一个合适的值。这里我们选择最常用的10K电阻进行分压。我们可以根据电阻分压公式计算分压后的电压：

<img src="E:\076lxl\work\note4embedded\prj\assets\eq1.png" alt="img" style="zoom:33%;" />

其中 【**R**】是电量测量接口图中的R7，10K；**【R总】**是R6+R7为20K； **【U源】**是 BAT+，即电池电压。那么**当电池满电为4.2V时**，两个电阻分压的结果U为：

**U = (10K** **/ 20K** **) \* 4.2V = 2.1V**

2.1V是完全符合我们的ADC测量范围。我们再计算**当电池要没有电为3.2V时**，两个电阻分压的结果U为：

**U = (10K** **/ 20K** **) \* 3.2V = 1.6V**

## 屏幕驱动

GD32F470的SPI存在FIFO，所以发送数据的结尾需要等待数据发送完成再释放片选。否则容易导致屏幕显示出现问

题（一般都是**以缓冲区是否为空当作传输的判断标准，以此提高发送效率**）。

硬件SPI与软件SPI相比，**硬件SPI**是靠硬件上面的SPI控制器，所有的时钟边缘采样，时钟发生，还有时序控制，都是由硬件完成的。它**降低了CPU的使用率，提高了运行速度**。**软件SPI就是用代码控制IO输出高低电平，模拟SPI的时序**，这种方法通信速度较慢，且不可靠。

### 从LCD原厂代码移植

1. 将源码导入工程；
2. 根据编译报错处进行粗改；
3. 修改引脚配置；
4. 修改时序配置；
5. 移植验证。