# 服务器入门知识

定义：为网络、数据库等应用提供服务的高性能计算机

组成：多核cpu、存储器（内存DDR、硬盘）、网卡...

总线：UPI、PCIe、SPI

![](https://prd.fs.huaqin.com/space/api/box/stream/download/asynccode/?code=NjIzNDllZGQ5ZDg2MjNmZDc4MjgwNzgzODc0MzI0YjVfcmEzVlA0ZVppNFROdkxhSEJvSDZSVkdONGNXYkhUdk5fVG9rZW46Ym94aHFBNkMxa20zM0liODVkNUJUbUF4dTdlXzE3NTEzMzI5NzM6MTc1MTMzNjU3M19WNA)

---

# CPP

## 设计模式

## **1. 单例模式（Singleton）**

**作用**：确保一个类**只有一个实例**，并提供全局访问点。

**应用场景**：配置管理、日志系统、线程池等需要全局唯一对象的场景。

### **C++** **实现（懒汉式，线程安全）**

```C++
#include <iostream>#include <mutex>class Singleton {private:    static Singleton* instance; // 静态实例    static std::mutex mtx;     // 互斥锁（保证线程安全）    // 私有构造函数，防止外部实例化    Singleton() {}public:    // 删除拷贝构造和赋值运算符    Singleton(const Singleton&) = delete;    Singleton& operator=(const Singleton&) = delete;    // 获取单例实例    static Singleton* getInstance() {        std::lock_guard<std::mutex> lock(mtx); // 加锁        if (instance == nullptr) {            instance = new Singleton();        }        return instance;    }    void showMessage() {        std::cout << "Singleton instance!" << std::endl;    }};// 静态成员初始化Singleton* Singleton::instance = nullptr;std::mutex Singleton::mtx;int main() {    Singleton* s1 = Singleton::getInstance();    Singleton* s2 = Singleton::getInstance();    s1->showMessage(); // 输出: Singleton instance!    std::cout << "s1 == s2: " << (s1 == s2) << std::endl; // 输出: 1（相同实例）    return 0;}
```

**优点**：

- 全局唯一访问，避免重复创建对象。

- 延迟初始化（首次调用时才创建）。

**缺点**：

- 需处理多线程安全问题（如用 `mutex`）。

- 可能引入不必要的全局状态。

---

## **2. 工厂模式（Factory Method）**

**作用**：定义一个创建对象的接口，但由子类决定实例化哪个类。

**应用场景**：需要动态创建不同子类对象时（如不同数据库连接、UI 控件）。

### **C++ 实现（抽象工厂方法）**

```C++
#include <iostream>// 抽象产品class Product {public:    virtual void use() = 0;    virtual ~Product() = default;};// 具体产品Aclass ConcreteProductA : public Product {public:    void use() override {        std::cout << "Using Product A" << std::endl;    }};// 具体产品Bclass ConcreteProductB : public Product {public:    void use() override {        std::cout << "Using Product B" << std::endl;    }};// 抽象工厂class Factory {public:    virtual Product* createProduct() = 0;    virtual ~Factory() = default;};// 具体工厂A（生产ProductA）class ConcreteFactoryA : public Factory {public:    Product* createProduct() override {        return new ConcreteProductA();    }};// 具体工厂B（生产ProductB）class ConcreteFactoryB : public Factory {public:    Product* createProduct() override {        return new ConcreteProductB();    }};int main() {    Factory* factoryA = new ConcreteFactoryA();    Product* productA = factoryA->createProduct();    productA->use(); // 输出: Using Product A    Factory* factoryB = new ConcreteFactoryB();    Product* productB = factoryB->createProduct();    productB->use(); // 输出: Using Product B    delete productA;    delete productB;    delete factoryA;    delete factoryB;    return 0;}
```

**优点**：

- 解耦对象创建和使用。

- 支持扩展（新增产品只需新增工厂）。

**缺点**：

- 每新增一个产品，就要新增一个工厂类，可能增加代码量。

---

## **3. 观察者模式（Observer）**

**作用**：定义对象间的**一对多依赖** ，当一个对象状态改变时，所有依赖它的对象都会自动收到通知。

**应用场景**：事件处理系统、消息订阅、GUI 组件交互。

### **C++ 实现**

```C++
#include <iostream>#include <vector>#include <algorithm>// 观察者接口class Observer {public:    virtual void update(const std::string& message) = 0;    virtual ~Observer() = default;};// 具体观察者class ConcreteObserver : public Observer {private:    std::string name;public:    ConcreteObserver(const std::string& name) : name(name) {}    void update(const std::string& message) override {        std::cout << name << " received: " << message << std::endl;    }};// 被观察者（主题）class Subject {private:    std::vector<Observer*> observers;public:    void attach(Observer* observer) {        observers.push_back(observer);    }    void detach(Observer* observer) {        observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());    }    void notify(const std::string& message) {        for (Observer* observer : observers) {            observer->update(message);        }    }};int main() {    Subject subject;    ConcreteObserver observer1("Observer 1");    ConcreteObserver observer2("Observer 2");    subject.attach(&observer1);    subject.attach(&observer2);    subject.notify("Hello World!");    // 输出:    // Observer 1 received: Hello World!    // Observer 2 received: Hello World!    subject.detach(&observer1);    subject.notify("Goodbye!");    // 输出: Observer 2 received: Goodbye!    return 0;}
```

**优点**：

- 解耦观察者和被观察者。

- 支持动态添加/删除观察者。

**缺点**：

- 如果观察者过多，可能影响性能。

- 需注意循环引用问题（如用 `weak_ptr` 避免内存泄漏）。

---

## **总结**

|          |         |                           |             |
| -------- | ------- | ------------------------- | ----------- |
| **模式**   | **作用**  | **C++ 关键点**               | **适用场景**    |
| **单例**   | 全局唯一实例  | `static` + 线程安全 (`mutex`) | 配置管理、日志系统   |
| **工厂方法** | 动态创建对象  | 抽象工厂 + 具体工厂               | 数据库连接、UI 控件 |
| **观察者**  | 一对多事件通知 | `Subject` + `Observer` 接口 | 消息订阅、GUI 事件 |

这些模式在 C++ 中广泛应用，合理使用可以大幅提升代码的**可维护性和扩展性** 。

# Linux

## 启动

在嵌入式系统中，**Boot ROM（片上引导只读存储器）** 是**可以被CPU直接寻址并执行其中代码的**。其核心原因在于，Boot ROM的地址空间被**静态映射到CPU的地址总线范围内**，且CPU复位后的初始执行地址（复位向量）通常指向Boot ROM的起始位置。以下从技术细节展开说明：

**一、Boot ROM的地址映射机制**

CPU的地址空间是一个线性的地址范围（如32位CPU支持4GB地址空间），其中不同的地址区间会被分配给不同的硬件资源（如片上RAM、外设寄存器、外部DDR内存、片外Flash等）。Boot ROM作为芯片内部的固定存储模块，其地址范围在芯片设计时就被**静态映射到CPU的地址空间中**（通常是低地址区域）。

例如：

- 大多数ARM Cortex-A系列处理器的复位向量地址（即CPU复位后PC寄存器的初始值）为 **0x00000000**，而这个地址通常被映射到片上Boot ROM的起始位置；

- 部分RISC-V架构的芯片（如SiFive系列）也会将复位向量（如0x10000000）映射到Boot ROM的地址范围。

**二、CPU如何直接执行Boot ROM中的代码**

当设备上电或复位时，CPU的程序计数器（PC）会被重置为**复位向量地址**（由芯片设计固定）。由于该地址已映射到Boot ROM的起始位置，CPU会从Boot ROM中读取第一条指令并执行，这一过程无需额外的软件干预。

具体流程如下：

1. **设备复位**：电源稳定后，CPU进入复位状态，PC被设置为复位向量地址（如0x00000000）；

2. **读取指令**：CPU通过地址总线发送复位向量地址，地址译码逻辑将其映射到Boot ROM的存储单元；

3. **执行代码**：CPU从Boot ROM中读取指令（如“初始化时钟”“检测启动设备”等）并逐条执行，完成最底层的引导逻辑。

**三、Boot ROM与其他存储介质的区别**

与外部Flash（如SPI NOR、NAND Flash）或DDR内存不同，Boot ROM的地址映射是**片内固定且无需初始化的**，因此CPU可以直接访问，无需提前配置存储控制器或内存控制器。

例如：

- 外部DDR内存需要先通过Boot ROM或SPL完成“内存训练”（如配置时序、校准参数）后，CPU才能访问其地址空间；

- 外部Flash需要通过片上的Flash控制器（如SPI控制器、NAND控制器）初始化后，CPU才能通过控制器接口间接访问；

- 而Boot ROM的地址映射是芯片出厂时就固定的，CPU复位后可直接访问，无需任何初始化步骤。

**四、特殊场景下的限制**

虽然Boot ROM可被直接寻址执行，但在实际系统中可能存在以下限制：

1. **只读属性**：Boot ROM的存储单元是固化的（掩膜ROM），CPU只能读取指令，无法修改其中的内容；

2. **地址空间覆盖**：部分芯片在启动后可能通过MMU（内存管理单元）重新映射地址空间，将原Boot ROM的地址区间覆盖为其他存储（如将0x00000000映射到DDR内存），但这是软件配置的结果，不影响Boot ROM本身的可寻址性；

3. **安全机制**：某些高安全芯片（如支持Secure Boot的SoC）可能通过硬件锁（如熔丝）或安全扩展（如ARM TrustZone）限制非特权模式代码访问Boot ROM，但CPU在特权模式（如复位后的初始模式）下仍可直接执行其中的代码。

**总结**

Boot ROM是**CPU可以直接寻址并执行的片上只读存储器**，其地址空间在芯片设计时被静态映射到CPU的地址总线范围内，且复位向量地址指向其起始位置。这一设计确保了设备上电后能立即执行最底层的引导代码，是嵌入式系统启动流程的“起点”。

> 链路：
> 
> 晶振硬件地址流->FLASH->数据流->芯片内部SRAM->外部DRAM（快、存储量高）->初始化系统与外设
> 
> 多核：第0个CPU的第0个核开始->链式启动
> 
> 保密性：多发送验证

---

## Make

[2023-09-03Linux命令详解./configure、make、make install 命令 - 简书](https://www.jianshu.com/p/9af7fce3c4bb)

---

## 指令

- Tar gxzf 。。。.gz

- su修改解包出来的文件权限=>自己

- tree -L 2 查看二级目录

- find . | grep fru 在所有目录中寻找fru关键词的文件

- find . -name phosphor-ipmi-fru$ （以$结尾的文件

- cat [选项][文件] 查看目标文件的内容

- ll查看文件及详细内容 如权限
  
     **文件权限解释**
  
  - **第一个字符**：文件类型
    
    - `d`：目录
    
    - `-`：普通文件
    
    - `l`：符号链接
    
    - `s`：特殊文件
  
  - **后续9个字符**：权限分组（每3个一组）
    
    ```Plain
    rwx r-x r-x│   │   └─ 其他用户权限│   └─ 所属组权限  └─ 所有者权限
    ```
    
    - `r` = 可读（4）
    
    - `w` = 可写（2）
    
    - `x` = 可执行（目录的`x`表示可进入）

---

# Yocto

概念

- OpenEmbedded - 构建系统框架

- Poky - 嵌入式操作系统定制模板

- BitBake - 构建工具

- devtool - 开发工具

## 框架构成

框架：yocto->pocky->基于open embedded的bitbake交叉编译框架

layer->config文件定义.bb文件

![](https://prd.fs.huaqin.com/space/api/box/stream/download/asynccode/?code=OGM1ZWJjMTgyMTA2MjMwYjFiYmQ4ZTg1YzVjMmNmODRfZmpWRWhFVmsyN0xmVmdBWWJXVzQ5andLWFdld1hrMk1fVG9rZW46Ym94aHFXdjh1cm90cVFhR1FMWnZjUlkwN2NkXzE3NTEzMzI5NzM6MTc1MTMzNjU3M19WNA)

![](https://prd.fs.huaqin.com/space/api/box/stream/download/asynccode/?code=NWIzNTM1YTUxMjYzYWEwYTdkNzliYzNjMDlkYTMzNGRfemtHOU5qejhNSHF4NnlSN3Q0VWlXaGxIdncxMnM0NElfVG9rZW46Ym94aHFtQ2laMnFlOHlRWG85QlVzUTAzUUplXzE3NTEzMzI5NzM6MTc1MTMzNjU3M19WNA)

yocto(包含开源内核 提供recipe config layer配置文件集合）

+编译工具bitbake

+特定软件包+IPMI、redfish协议栈=>openbmc

？？recipe依赖class构建=>layer组织架构 +config文件 =>可利用bitbake解析

## 核心组件关系图

```Plain
BitBake (构建引擎)│└─OpenEmbedded (构建系统框架)   │   ├─元数据 (Metadata)   │  ├─层 (Layers)                   =====模块化容器   │  │  ├─菜谱 (Recipes, .bb文件)     =====软件包构建说明书   │  │  └─配置文件 (.conf)   │  └─类文件 (.bbclass)   └─配置 (Local Configuration)
```

## 各组件关系

### BitBake

- **角色**：构建引擎/任务执行器 =>**编译工具**

- **功能**：解析菜谱，执行任务依赖关系图

- **关系**：
  
  - 是OpenEmbedded和Yocto项目的底层引擎
  
  - 处理所有元数据文件(菜谱、配置、类)

### OpenEmbedded

- **角色**：构建系统框架 =>内核核心

- **功能**：提供构建嵌入式Linux系统的基础架构

- **关系**：
  
  - 使用BitBake作为执行引擎
  
  - 定义元数据的组织结构和标准
  
  - 提供核心层(meta-openembedded)

### 元数据(Metadata)

- **角色**：构建系统的"知识库"

- **包含**：
  
  - 菜谱(.bb)
  
  - 配置文件(.conf)
  
  - 类文件(.bbclass)
  
  - 包含文件(.inc)

- **关系**：
  
  - 被BitBake解析和执行
  
  - 通过层(Layers)组织
  
  - 包含构建系统的所有配置和指令

### 菜谱(Recipes)

- **角色**：软件包构建说明书

- **格式**：.bb文件 .bbappend (+patch

- **关系**：
  
  - 是**元数据的主要组成部分**
  
  - 存放在各层中
  
  - 被BitBake解析执行
  
  - 遵循OpenEmbedded定义的格式标准

### 层(Layers)

- **角色**：元数据的**模块化容器**

- **功能**：将相关**元数据分组管理**

- **关系**：
  
  - 包含菜谱、配置等元数据
  
  - 可以叠加(层间有优先级)
  
  - 典型层：
    
    - meta (Yocto项目核心)
    
    - meta-openembedded (OE核心)
    
    - meta-<vendor> (供应商层)
    
    - meta-<custom> (自定义层)

### 配置(config)

Local 构建环境的本地配置

bblayer 告知编译器文件地址

Build/temp

deploy/images/romulus->flash-romulus->qume

### 协作流程示例

1. 用户运行`bitbake <target>`

2. BitBake查找各层中的元数据

3. 解析相关菜谱和依赖关系

4. 根据OpenEmbedded定义的规则执行构建

5. 生成最终的镜像或软件包

### 类比说明

可以把这看作一个厨房系统：

- **BitBake构建引擎**：厨师长，负责协调所有工作

- **OpenEmbedded框架**：标准烹饪方法体系

- **元数据**：所有食谱和厨房规范的总和

- **菜谱**：单个菜肴的详细做法

- **层**：不同的食谱书(中式、西式、甜点等)

## 构建流程

Yocto的构建流程如下图所示，开发者通过指定一些用户配置文件(例如local.conf)，其中包含针对不同软件的配方文件、层， 目标开发板的配置文件和打包策略配置文件等。Yocto的执行引擎Bitbake会去读取这些配方和配置文件

![](https://prd.fs.huaqin.com/space/api/box/stream/download/asynccode/?code=Y2E1NjgxNjAzZjg3NDE0NzM2MjgxNzUyMTc1YTZhOGNfeUhZM3BmYUFJNXRhZXFSTmR4NmdLNWpTc2VsWkNzaXBfVG9rZW46Ym94aHFtVE1PcThmTGZiY05NbnZZYWUwRnBiXzE3NTEzMzI5NzM6MTc1MTMzNjU3M19WNA)

1. 去配方和配置文件指定的地方下载源代码(source fetching)。

2. 下载后，将源码提取到本地工作区中，在工作区中打补丁(patch application)、执行配置和编译等步骤

3. 分析软件包如何拆分成子包以及软件包之间的关系

4. 根据打包策略生成具体的软件包（deb, rpm或者ipk包）

5. 对软件包进行测试(QA tests)，包括一些通用的质量检查和健全性检查

6. 以软件包为单位生成Linux镜像（系统构建）和软件开发套件(SDK->应用开发)

## Bitbake

## Yocto构建

### 1.docker启动镜像

（包含工具链、库文件和系统环境，conda只是包版本环境管理）

docker start "yocto_build"

docker exec -it "yocto_build" /bin/bash

```Bash
sudo usermod -aG docker $USERdocker 
run -it --name yocto_build ubuntu:24.04 /bin/bash
# or sudo docker run -it --name yocto_build ubuntu:24.04 /bin/bash
```

在 **Yocto Project 构建环境** 中常用的 **依赖安装与本地化配置命令**，主要用于在 Ubuntu/Debian 系统（如你之前通过 Docker 启动的 `ubuntu:24.04` 容器）中准备构建 Yocto 镜像所需的工具链、库文件和系统环境。下面分两部分详细解释：

### 2.安装 Yocto 构建依赖

这是通过 `apt-get` 包管理器安装一系列工具和库，这些都是 Yocto Project 官方推荐的 **必备依赖**（不同 Yocto 版本可能略有差异，但核心工具一致）。命令中部分工具可能因换行或拼写问题需要调整（比如 `Iz4` 应为 `lz4`），先修正后再解释：

```Bash
apt-get install -y \  build-essential libglib2.0-dev libfdt-dev 
libpixman-1-dev \  zlib1g-dev ninja-build python3 python3-pip 
gcc g++ make \  file wget gawk diffstat bzip2 cpio 
chrpath zstd lz4 bzip2 vim \  git zlib1g-dev ninja-build 

sudo net-tools iputils-ping localeslocale-gen en_US.UTF-8  
# 生成 UTF-8 语言环境
```

### 3.编译和安装 QEMU

```Bash
cd qemu./configure -- target-list=arm-softmmu -- enable-debugmake -j$(nproc)
make installls//查看构建的qemu支持哪些CPUqemu-system-arm -M help
```

（针对 ARM 架构的模拟器），并最终验证构建结果。QEMU 是一款开源的硬件模拟器，常用于嵌入式开发（如 Yocto 项目中模拟 ARM 开发板）。下面分步骤详细解释：

#### 整体流程概览

1. 进入 QEMU 源码目录 → 2. 配置编译选项（**指定 ARM 架构、启用调试**）→ 3. 编译源码 → 4. 安装 QEMU → 5. 验证安装（查看支持的 ARM 开发板型号）。

#### 分步解释

2. `cd qemu`
- **作用**：进入之前通过 `git clone` 下载的 QEMU 源码目录（假设源码克隆到了当前目录下的 `qemu` 文件夹）。

- **前提**：需先成功克隆 QEMU 源码（如 `git clone` `https://github.com/qemu/qemu.gi``t`），否则会提示 `No such file or directory`。
3. `./configure --target-list=arm-softmmu --enable-debug`
- **作用**：**配置 QEMU 编译参数**，决定编译哪些组件、启用哪些功能。

- **核心参数详解**：
  
  - `--target-list=arm-softmmu`
    
    - `target-list`：指定要编译的目标架构和模式。
    
    - `arm`：目标架构为 **ARM**（嵌入式开发最常用的架构之一，如 ARM Cortex-A/R/M 系列）。
    
    - `softmmu`：全称 "Software Memory Management Unit"，表示编译 **系统级模拟器**（可模拟整个计算机系统，包括 CPU、内存、外设，如模拟一块 ARM 开发板）。
      
      - 与之对应的是 `user` 模式（如 `arm-linux-user`），用于模拟单个用户态程序（而非整个系统）。
    
    - **效果**：仅编译 ARM 架构的系统级模拟器（生成 `qemu-system-arm` 可执行文件），其他架构（如 x86、MIPS）不编译，减少编译时间和体积。
  
  - `--enable-debug`
    
    - 启用 **调试模式**，在编译时保留调试符号（便于使用 `gdb` 调试 QEMU 本身），并可能开启更多日志输出。
    
    - 适合开发/调试 QEMU 或基于 QEMU 的嵌入式系统（如 Yocto 镜像调试），但会略微降低性能，生产环境可省略此参数。

- **其他隐含操作**： `configure` 脚本会检查系统依赖（如编译器、库文件），若缺少依赖（如 `libglib2.0-dev`、`libpixman-1-dev` 等），会报错并提示需要安装的包（这也是之前需要安装大量依赖的原因）。
4. `make -j$(nproc)`
- **作用**：**编译 QEMU 源码**，生成可执行文件（如 `qemu-system-arm`）。

- **参数解释**：
  
  - `make`：调用 Make 工具执行编译（基于 `configure` 生成的 Makefile）。
  
  - `-j$(nproc)`：启用 **多线程编译**，加速编译过程。
    
    - `$(nproc)`：是 Linux 命令，用于获取当前系统的 CPU 核心数（如 4 核 CPU 会返回 `4`）。
    
    - `-j4` 表示同时使用 4 个线程编译，核心数越多，编译速度越快（根据实际 CPU 调整，避免资源耗尽）。

- **编译结果**：编译完成后，可在 `qemu/build` 或源码根目录下找到 `qemu-system-arm` 可执行文件（具体路径取决于 QEMU 版本）。
5. `make install`
- **作用**：**将编译好的 QEMU 安装到系统目录**（默认路径为 `/usr/local/bin/`、`/usr/local/share/qemu/` 等），使其成为系统全局可用的命令。

- **细节**：
  
  - 安装后，可直接在终端输入 `qemu-system-arm` 调用，无需指定路径。
  
  - 若不想安装到系统目录，可省略此步，直接通过源码目录下的 `qemu-system-arm` 执行（如 `./qemu-system-arm`）。
  
  - 可能需要 `sudo` 权限（因 `/usr/local/` 目录通常需要管理员权限写入）：
  
  - bash

```Bash
sudo make install
```

6. `qemu-system-arm -M help`
- **作用**：**验证 QEMU ARM 模拟器是否安装成功，并查看其支持的 ARM 开发板/机器型号**

- **参数解释**：
  
  - `qemu-system-arm`：ARM 系统级模拟器的可执行文件名（由 `--target-list=arm-softmmu` 编译生成）。
  
  - `-M help`：`-M`（或 `--machine`）指定要模拟的机器型号，`help` 表示列出所有支持的机器型号。

- **预期输出**：
  会打印一长串支持的 ARM 开发板/SoC 型号，例如：

```Plaintext
Supported machines are:...virt                 QEMU 2.12+ ARM Virtual Machine (alias of virt-7.2)virt-7.2             QEMU 7.2 ARM Virtual Machinevexpress-a15         ARM Versatile Express for Cortex-A15vexpress-a9          ARM Versatile Express for Cortex-A9raspberrypi          Raspberry Pi (revision 2)...
```

- 这些型号对应不同的 ARM 开发板（如 Versatile Express、树莓派等），后续可通过 `-M <型号>` 来模拟特定硬件（如 `qemu-system-arm -M vexpress-a9` 模拟 Cortex-A9 开发板）。

#### 与 Yocto 构建的关系

在 Yocto Project 中，QEMU 通常用于 **模拟目标嵌入式系统**（如在 x86 电脑上运行 ARM 架构的 Yocto 镜像）。通过上述命令编译的 `qemu-system-arm`，可以直接加载 Yocto 生成的 `.img` 镜像文件，验证系统功能，例如：

bash

```Bash
qemu-system-arm -M vexpress-a9 -kernel zImage -dtb vexpress-v2p-ca9.dtb -sd rootfs.img -serial stdio
```

#### 常见问题

1. **编译报错“缺少依赖”**：需确保之前安装了所有依赖（如 `build-essential`、`libglib2.0-dev` 等，见你之前的 `apt-get install` 命令）。

2. **`qemu-system-arm: command not found`**：未执行 `make install`，或安装路径未加入系统 `PATH`（可手动添加：`export PATH=/usr/local/bin:$PATH`）。

3. **支持的机器型号过少**：可能是 QEMU 版本过旧，建议克隆最新源码或使用 `--enable-all-targets` 编译所有架构（但耗时较长）。

### 4.用户配置

```Bash
//创建一个用户
useradd -m -s /bin/bash {username}
//给用户增加sudo权限
usermod -aG sudo {username}//配置用户密码
passwd {username}//切换到目标用户  以后直接进入就可以su {username}
//配置git账号
cd ~   // 快速回到根目录
echo https://{ID}:{KEY}@github.com >> .git-credentials
git config --global credential.helper store
```

### 5.mata-freescale

```Bash
git clone git://git.yoctoproject.org/pokycd poky
git clone https://github.com/Freescale/meta-freescale.git
```

`meta-freescale` 是 Yocto/OE（OpenEmbedded）项目中的一个 **BSP（Board Support Package）层**，主要为 NXP（原 Freescale）的 i.MX 和 QorIQ 处理器提供支持。

### 6.配置yocto构建系统

```Bash
source oe-init-build-env build
```

> insert为编辑模式，esc退出，:wq保存、退出vim

- 修改build/conf/local.conf配置：

machine image_fstypes

- 修改conf/bblayers.conf配置：

BBLAYERS + 新clone的meta层

```Bash
bitbake core-image-minimal
```

// 网络原因无法clone （报错do_fetch unpack

**流程！！**

**fetch->unpack->patch->config->compile->install->package->image**

# openbmc

## openbmc编译

1. **编译特定芯片软件包**

openbmc/openbmc-lincolncity/

`Source setup intel-ast2600`

// 将openbmc中所有有关此类芯片的配置（每个软件包中都有不同的芯片架构arch）编译

2. **修改**

openbmc/openbmc-lincolncity/build/intel-ast2600/

**`vim conf/local.conf`**

insert(i)模式下修改

`BB_NUMBER_THREADS = "8" // 线程数量`

`PARALLEL_MAKE = "-j 8"`

`DL_DIR="/home/openbmc/downloads"`

`MACHINE ??= "intel-ast2600"`

Esc :wq保存退出

3. **bitbake编译**

openbmc/openbmc-lincolncity/build/intel-ast2600/

**`bitbake intel-platforms`**

+ sstate-cache symbol tmp
- **tmp**：存放构建过程中的所有临时文件、中间文件和任务输出。
  
  - **`work/`**:
    
    - 每个配方的构建工作目录（如 `x86_64-linux、armv7ahf-vfpv4d16-openbmc-linux-gnueabi`）。
    
    - 包含源码解压、补丁应用、编译生成的中间文件（如 `obj/`、`temp/`）。
    
    - 任务日志（`log.do_<task>`）也存储在此处。
    
    > log文件包含log./run. 如do_compile过程中会出现的log输出

- **`sysroots/`**:
  
  - 目标设备（`sysroots/<target>`）和主机（`sysroots/<host>`）的依赖库和头文件。
  
  - 用于交叉编译时的环境隔离。

- **`deploy/`**:
  
  - **最终生成的镜像**（如 `.wic`、`.ext4`）、软件包（`.ipk`、`.deb`）和 SDK。

- **`stamps/`**:
  
  - 记录任务完成状态的标记文件（如 `.do_fetch.done`），用于增量构建。

- sstate-cache：存储共享**状态缓存**（Shared State Cache），用于加速后续构建。

> Devtool modify会在workspace内新生成软件包，在其中修改。
> 
> 若存在修改siginfo，生成新哈希值，如果没有修改哈希值不变，bitbake无需再rebuild。
> 
> （dpsk：通过校验和（如 `taskhash`）判断是否可复用缓存，避免重复编译。

- **流程**:
  
  - `tmp/work/` 完成源码编译 → 生成结果存入 `tmp/deploy/` → 缓存到 `sstate-cache/`。
  
  - 下次构建时，优先从 `sstate-cache/` 复用缓存，避免重复工作。
4. **进入tmp/work目录找寻fru模块**

openbmc/openbmc-lincolncity/build/intel-ast2600/tmp/work

**`tree -L 2 | grep fru`**

├── default-fru

├── phosphor-ipmi-fru

├── phosphor-ipmi-fru-inventory-example-native

├── phosphor-ipmi-fru-merge-config-native

├── phosphor-ipmi-fru-properties-native

├── phosphor-ipmi-fru-read-inventory-example-native

├── phosphor-ipmi-fru-whitelist-native

- 构建时： `*-native` 工具合并 `default-fru` 模板 + 硬件厂商配置 → 生成最终的 `fru-config.bin`

- 运行时： `phosphor-ipmi-fru` 服务读取 `fru-config.bin` 和实际 EEPROM 数据 → 通过 IPMI 接口提供 FRU 信息

**`find . -name phosphor-ipmi-fru$`**

cd进openbmc/openbmc-lincolncity/build/intel-ast2600/tmp/work/armv7ahf-vfpv4d16-openbmc-linux-gnueabi/phosphor-ipmi-fru/1.0+gitAUTOINC+440d84d4fe-r1 （版本

> armv7文件夹底下还有很多模块 phosphor一堆还没理解都是些啥（！！

**文件内容：**

|                             |     |                                                        |
| --------------------------- | --- | ------------------------------------------------------ |
| 文件/目录名                      | 类型  | 作用                                                     |
| `build/`                    | 目录  | 编译生成的临时文件（如.o、.so）                                     |
| `debugsources.list`         | 文件  | 调试符号文件的索引列表（cat一堆头文件依赖？                                |
| `deploy-rpms/`              | 目录  | 构建生成的RPM包存放位置                                          |
| `deploy-source-date-epoch/` | 目录  | 记录源码时间戳（用于可复现构建）                                       |
| `git/`                      | 目录  | **从Git仓库克隆的源码（****`devtool modify`** **生成）（git log可看** |
| `image/`                    | 目录  | 最终生成的镜像文件（如BMC固件）                                      |
| `license-destdir/`          | 目录  | 许可证文件集合                                                |
| `meson.cross`               | 文件  | 交叉编译的Meson构建配置                                         |
| `meson.native`              | 文件  | 本地编译的Meson构建配置                                         |
| `meson-qemuwrapper`         | 文件  | QEMU模拟器的包装脚本                                           |
| `obmc-read-eeprom@.service` | 文件  | Systemd服务模板（用于读取EEPROM）                                |
| `of-name-to-eeprom.sh`      | 文件  | OpenFirmware设备名到EEPROM路径的转换脚本                          |
| `package/`                  | 目录  | 包内容的临时安装目录                                             |
| `packages-split/`           | 目录  | 按包拆分后的文件（用于生成多个子包）                                     |
| `phosphor-ipmi-fru.spec`    | 文件  | RPM包的构建规范文件                                            |
| `pkgdata/`                  | 目录  | 包依赖关系数据                                                |
| `pkgdata-pdata-input/`      | 目录  | 包元数据输入文件                                               |
| `pkgdata-sysroot/`          | 目录  | 系统根目录下的包数据                                             |
| `pseudo/`                   | 目录  | 伪（fakeroot）环境数据                                        |
| `recipe-sysroot/`           | 目录  | 目标系统的根目录（包含依赖库）                                        |
| `recipe-sysroot-native/`    | 目录  | 主机工具的根目录（用于交叉编译）                                       |
| `source-date-epoch/`        | 目录  | 源码时间戳（与`deploy-source-date-epoch/` 关联）                 |
| `spdx/`                     | 目录  | SPDX格式的软件物料清单（SBOM）                                    |
| `temp/`                     | 目录  | 临时构建日志和中间文件（run+log                                    |

**git下****`tree`**

```Plain
└── usr    ├── bin    │   ├── of-name-to-eeprom.sh    │   └── phosphor-read-eeprom    ├── lib    │   ├── host-ipmid    │   │   └── libstrgfnhandler.so.1 -> ../ipmid-providers/libstrgfnhandler.so.1    │   ├── ipmid-providers    │   │   ├── libstrgfnhandler.so -> libstrgfnhandler.so.1    │   │   ├── libstrgfnhandler.so.1 -> libstrgfnhandler.so.1.0    │   │   └── libstrgfnhandler.so.1.0    │   ├── libwritefrudata.so -> libwritefrudata.so.1    │   ├── libwritefrudata.so.1 -> libwritefrudata.so.1.0    │   ├── libwritefrudata.so.1.0    │   └── systemd    │       ├── system    │       │   └── obmc-read-eeprom@.service    │       └── system-preset    │           └── 98-phosphor-ipmi-fru.preset    └── src        └── debug            └── phosphor-ipmi-fru                └── 1.0+gitAUTOINC+440d84d4fe-r1                    ├── extra-properties-gen.cpp                    ├── fru_area.cpp                    ├── fru_area.hpp                    ├── fru-gen.cpp                    ├── frup.cpp                    ├── frup.hpp                    ├── readeeprom.cpp                    ├── strgfnhandler.cpp                    ├── types.hpp                    ├── writefrudata.cpp                    └── writefrudata.hpp12 directories, 22 files➜  package git:(bhs-24.09) cat ../obmc-read-eeprom@.service [Unit]Description=Read %I EEPROMWants=mapper-wait@-xyz-openbmc_project-inventory.serviceAfter=mapper-wait@-xyz-openbmc_project-inventory.serviceStartLimitBurst=10[Service]Restart=on-failureType=oneshotEnvironmentFile={envfiledir}/obmc/eeproms/%IExecStartPre={bindir}/of-name-to-eeprom.sh {envfiledir}/obmc/eeproms/%IExecStart=/usr/bin/env phosphor-read-eeprom --eeprom $SYSFS_PATH --fruid $FRUIDSyslogIdentifier=phosphor-read-eeprom[Install]WantedBy=multi-user.target
```

package+split没看懂

pseudo目录是 Yocto 构建系统中 `fakeroot`（伪 root 环境）的核心工作目录，用于在非 root 用户下模拟 root 权限操作（如文件权限修改、设备节点创建等）。

5. 模块架构理解

openbmc/openbmc-lincolncity/build/intel-ast2600/

devtool modify phosphor-ipmi-fru

```Bash
# 输出INFO: Adding local source files to srctree...INFO: Source tree extracted to /home/lingxl/code/git/openbmc/openbmc-lincolncity/build/intel-ast2600/workspace/sources/phosphor-ipmi-fruINFO: Recipe phosphor-ipmi-fru now set up to build from /home/lingxl/code/git/openbmc/openbmc-lincolncity/build/intel-ast2600/workspace/sources/phosphor-ipmi-fru
```

intel-ast2600就会从tmp/work/目标架构/模块软件包/版本号迁移到

workspace/sources/模块

sstate-cache记录修改变化（使用哈希表示 是每一个模块都变化都记录？？？

openbmc/openbmc-lincolncity/build/intel-ast2600/workspace/sources/

`code phosphor-ipmi-fru`

---

## 模块架构理解

这是 OpenBMC 项目构建系统中针对 `armv7ahf-vfpv4d16-openbmc-linux-gnueabi` 架构的软件包列表，包含 BMC 固件的核心组件和工具。以下是分类解析：

### **1. 核心功能组件**

|                            |                            |
| -------------------------- | -------------------------- |
| 软件包名称                      | 功能描述                       |
| **phosphor-ipmi-fru**      | IPMI FRU 数据管理（硬件资产信息存储/读取） |
| **phosphor-ipmi-host**     | IPMI 主机接口服务（与主板通信）         |
| **phosphor-led-manager**   | LED 状态控制（电源/故障指示灯）         |
| **phosphor-network**       | 网络配置管理（IP/DHCP/VLAN）       |
| **phosphor-state-manager** | 系统状态机（控制开机/关机/重启流程）        |
| **bmcweb**                 | Redfish REST API 服务        |

---

### **2. 硬件驱动与管理**

|                           |                              |
| ------------------------- | ---------------------------- |
| 软件包名称                     | 对应硬件                         |
| **libpeci**               | Intel PECI 总线访问（CPU 温度/功耗监控） |
| **libmctp**               | MCTP 协议栈（管理控制器传输协议）          |
| **i2c-tools**             | I²C 设备调试工具                   |
| **mtd-utils**             | Flash 存储器操作工具（擦除/写入）         |
| **aspeed-mtd-partitions** | AST2600 等 BMC 芯片的 Flash 分区管理 |

---

### **3. 安全相关**

|                                  |                    |
| -------------------------------- | ------------------ |
| 软件包名称                            | 安全功能               |
| **phosphor-certificate-manager** | SSL 证书管理           |
| **shadow**                       | 用户密码加密工具           |
| **libseccomp**                   | 系统调用过滤（沙盒防护）       |
| **fips-openssl**                 | FIPS 140-2 认证的加密算法 |

---

### **4. 工具与依赖库**

|               |                       |
| ------------- | --------------------- |
| 软件包名称         | 用途                    |
| **sdbusplus** | D-Bus C++ 封装库（IPC 通信） |
| **boost**     | C++ 通用库（智能指针/线程等）     |
| **python3**   | Python 运行时（用于脚本管理）    |
| **sqlite3**   | 轻量级数据库（存储配置信息）        |
| **systemd**   | 初始化系统和服务管理            |

---

### **5. 厂商定制组件**

|                    |                        |
| ------------------ | ---------------------- |
| 软件包名称              | 厂商/平台特定功能              |
| **intel-ipmi-oem** | Intel 定制 IPMI 命令       |
| **pfr-manager**    | Intel PFR（平台固件恢复）支持    |
| **aspeed-vgpio**   | ASPEED BMC 的虚拟 GPIO 控制 |

### 依赖关系

```Plain
graph TD    phosphor-ipmi-fru --> sdbusplus    phosphor-ipmi-fru --> libpeci    bmcweb --> boost    bmcweb --> nlohmann-json    phosphor-state-manager --> systemd
```

1. **调试工具**：
   
   1. `ipmitool`：手动测试 IPMI 命令
   
   2. `i2c-tools`：硬件寄存器调试
   
   3. `liblogging`：日志系统配置

2. **代码位置**：
   
   ```Bash
   # 示例：查找 IPMI FRU 源码find . -name "*fru*.cpp" | grep phosphor-ipmi
   ```

3. **构建控制**：
   
   1. 在 `meta-phosphor` 层中通过 `.bbappend` 文件定制包配置
- **Q：如何添加新包？** 在 `meta-<layer>/recipes-<category>/` 下创建新的 `.bb` 配方文件

- **Q：如何排除不需要的包？** 在 `local.conf` 中使用：
  
  ```Bash
  PACKAGE_EXCLUDE += "package-name"
  ```

如需特定包的详细说明，请告知名称（如 `phosphor-ipmi-fru` 或 `entity-manager`）。

> 工程需要查看export出外部的函数

![](https://prd.fs.huaqin.com/space/api/box/stream/download/asynccode/?code=NmY5ZGU1ZDhmNTMyODY4MDJkYzQ0YWI3MjJmZGRjMzRfT3RORE90bjZnZFJqeklTcFVmSWtuaklvTEdwUHVSbk9fVG9rZW46Ym94aHFtWlJvdHY0YW1BdnAxWm1oY3hqM1ZnXzE3NTEzMzI5NzQ6MTc1MTMzNjU3NF9WNA)

这张图展示了智能平台管理逻辑设备（Intelligent Platform Management Logical Devices）的框架结构，主要用于描述智能平台管理控制器（Intelligent Platform Management Controller，IPMC）、智能平台管理总线（Intelligent Platform Management Bus，IPMB）、基板管理控制器（Baseboard Management Controller，BMC）以及系统软件（System Software）之间的交互关系。以下是对图中各个部分的详细解释：

#### 上：**智能平台管理控制器（IPMC）**

图中有两个IPMC（标记为A和B），它们是智能平台管理的核心组件，负责管理传感器（Sensors）、事件生成器（Event Generator）、IPM设备（IPM Device）等。每个IPMC都包含以下主要部分：

- **传感器设备（Sensor Device）**：用于监测系统的各种参数，如温度、电压等。

- **事件生成器（Event Generator）**：根据传感器数据或其他条件生成事件。

- **消息处理器（Message Handler）**：处理和转发各种管理消息。

- **IPMB消息接口（IPMB Message Interface）**：用于通过IPMB与其他设备通信。

#### 上-中：**智能平台管理总线（IPMB）**

IPMB是连接各个智能设备的总线，图中显示了IPMB连接了两个IPMC、基板管理控制器（BMC）以及其他非智能设备（Non-Intelligent Devices）。IPMB上的设备包括：

- **传感器（Sensors）**：提供系统状态信息。

- **IPM设备（IPM Device）**：执行特定的管理功能。

- **FRU库存EEPROM（FRU Inventory EEPROM）**：存储现场可更换单元（FRU）的信息。

#### 中：**基板管理控制器（BMC）**

BMC是一个独立的控制器，负责管理基板（Baseboard）上的各种设备和功能。图中BMC包含以下主要部分：

- **IPMB消息接口（IPMB Message Interface）**：通过IPMB与其他设备通信。

- **传感器设备（Sensor Device）**：监测基板上的传感器数据。

- **事件接收器（Event Receiver）**：接收来自IPMC或其他设备的事件。

- **SEL设备（SEL Device）**：管理系统事件日志（System Event Log）。

- **PEF设备（PEF Device）**：处理平台事件过滤（Platform Event Filtering）。

- **警报处理设备（Alert Processing Device）**：处理和生成警报。

- **应用设备（Application Device）**：执行特定的管理应用。

- **系统消息接口（System Message Interface）**：与系统软件通信。

#### 下：**系统软件（System Software）**

系统软件通过系统 - BMC接口（System - BMC Interface）与BMC通信，主要包括以下部分：

- **BIOS**：基本输入输出系统，负责系统启动和初始化。

- **SMI处理器（SMI Handler）**：处理系统管理中断（System Management Interrupt）。

- **操作系统（OS）**：提供系统的基本操作环境。

- **系统管理软件（System Management SW）**：执行高级系统管理功能。

#### **其他组件**

- **非智能设备（Non-Intelligent Devices）**：连接在IPMB上的非智能设备，如J2C传感器（J2C Sensor）。

- **PCI管理总线接口（PCI Mgmt Bus Interface）**：用于连接PCI管理总线。

- **串行接口（Serial Interface）**：提供串行通信功能。

- **网络接口（NIC If Ig）**：用于网络通信。

- **FRU库存设备（FRU Inventory Device）**：存储FRU信息。

- **SDR库存设备（SDR Inventory Device）**：存储传感器数据记录（Sensor Data Record）。

- **初始化代理（Initialization Agent）**：负责系统初始化。

### **通信路径**

- **IPMB通信**：通过IPMB连接各个智能设备，实现设备间的管理消息传递。

- **系统 - BMC接口**：系统软件通过此接口与BMC通信，获取系统状态信息和执行管理操作。

- **串行端口共享逻辑（Serial Port Sharing Logic）**：用于共享串行端口资源。

- **局域网（LAN）**：提供网络连接，实现远程管理。

总体而言，这个框架展示了一个复杂的系统管理架构，通过IPMC、IPMB、BMC和系统软件的协同工作，实现对系统的智能管理和监控。

## FRU模块

### **FRU 信息（Field Replaceable Unit 信息）**

**功能概述：**

IPMI（智能平台管理接口）规范支持存储和访问系统中不同模块的多组非易失性“现场可更换单元（FRU）”信息。在高端企业级系统中，通常每个主要硬件模块（如处理器板、内存板、I/O板等）都有独立的FRU数据。

**FRU 数据内容：**

- 序列号（Serial Number）

- 部件号（Part Number）

- 型号（Model）

- 资产标签（Asset Tag）

**访问方式与优势：**

1. **独立于主系统**：FRU信息可通过IPMB（智能平台管理总线）或管理控制器（如BMC）访问，无需依赖主处理器、BIOS、操作系统或系统软件。

2. **带外管理（Out-of-Band）**：即使系统断电，仍可通过IPMB、远程管理卡（如iRMC）或其他连接设备获取FRU信息。

3. **故障容错**：当主处理器故障导致常规访问机制失效时，IPMI仍能提供FRU数据，便于远程库存管理和自动化维护。

**与其他机制的兼容性：**

IPMI不替代SMBIOS或PCI VPD（重要产品数据）等现有FRU机制，而是作为补充，尤其在系统宕机时提供带外访问能力。

=>

### **FRU Inventory Device（FRU库存设备）**

**功能定义：**

FRU Inventory Device（FRU库存设备）是IPMI（智能平台管理接口）中用于访问特定硬件模块的**FRU信息**（如序列号、部件号、资产标签等）的接口。系统中每个主要硬件模块（如主板、CPU板、内存板、I/O扩展卡等）通常有**一组独立的FRU信息**，而访问这些信息的FRU Inventory Device可能有多个。

### **关键概念解析**

#### **1. FRU信息与模块的对应关系**

- 每个**主要硬件模块** （Field Replaceable Unit, FRU）都有自己的一组FRU信息（例如：厂商、型号、序列号、生产日期等）。

- **一个模块的FRU信息**可能通过**多个FRU Inventory Device**提供访问（例如：既可以通过BMC访问，又可以通过SEEPROM直接读取）。

#### **2. Primary FRU Inventory Device（主FRU库存设备）**

- **定义**：管理控制器（如BMC）所在的硬件模块（例如主板）的FRU信息存储设备。

- **作用**：
  
  - 存储管理控制器自身的FRU信息（例如BMC所在主板的序列号）。
  
  - 是IPMI管理体系中**默认的FRU信息源**，用于标识整个系统的核心模块。

---

### **典型应用场景**

1. **硬件资产追踪**
   
   1. 通过IPMI命令（如`ipmitool fru print`）读取FRU信息，实现远程资产管理和维护。
   
   2. 例如：服务器运维人员可以在不拆机的情况下，直接查询某块内存条的序列号或生产日期。

2. **故障诊断与替换**
   
   1. 当系统某模块（如电源、硬盘背板）故障时，可通过FRU信息快速定位问题硬件，并匹配替换部件。

3. **带外管理（Out-of-Band）**
   
   1. 即使主机操作系统崩溃，仍可通过BMC访问FRU信息，辅助故障分析。

---

### **示例：FRU信息的存储与访问方式**

|              |                                   |                               |
| ------------ | --------------------------------- | ----------------------------- |
| **硬件模块**     | **FRU信息存储方式**                     | **访问方式**                      |
| **主板（含BMC）** | Primary FRU Device（SEEPROM或Flash） | 通过BMC的IPMI命令（如`ipmitool fru`） |
| **内存板**      | 独立的24C02 SEEPROM                  | 通过IPMB或私有I²C总线读取              |
| **GPU加速卡**   | PCIe VPD（厂商自定义）                   | 通过OS工具（如`lspci -vv`）或IPMI     |

---

### **总结**

- **FRU Inventory Device** 是访问硬件模块FRU信息的接口，每个模块可能有多个访问途径。

- **Primary FRU Inventory Device** 特指管理控制器所在模块的FRU存储设备，是系统管理的核心。

- IPMI的FRU机制为服务器/硬件设备提供了**标准化、带外可访问的资产信息**，极大提升了运维效率。

（如果需要更具体的命令示例或硬件实现细节，可以进一步补充！）### **FRU Inventory Device（FRU库存设备）**

**功能定义：**

FRU Inventory Device（FRU库存设备）是IPMI（智能平台管理接口）中用于访问特定硬件模块的**FRU信息**（如序列号、部件号、资产标签等）的接口。系统中每个主要硬件模块（如主板、CPU板、内存板、I/O扩展卡等）通常有**一组独立的FRU信息**，而访问这些信息的FRU Inventory Device可能有多个。

### **FRU 设备**

IPMI 提供两种FRU信息存储方式：

#### **1. 通过管理控制器（Management Controller）**

- **访问方式**：使用IPMI命令间接访问非易失性存储设备（如Flash、EEPROM）。

- **优势**：硬件设计灵活，存储介质类型可由厂商自由选择（如NAND Flash、SPI EEPROM等）。

#### **2. 通过FRU SEEPROM（串行电可擦除存储器）**

- **设备类型**：兼容24C02标准的SEEPROM（基于I²C接口的非易失性存储芯片）。

- **适用场景**：
  
  - 无需为每个FRU配备独立的管理控制器，降低成本。
  
  - 可直接连接私有管理总线（Private Management Bus）或IPMB/PCI管理总线。

**注意事项：**

1. **数据完整性风险**：24C02类SEEPROM缺乏校验机制（如校验和），若直接部署在公共总线（如IPMB、PCI-SMBus）上，可能因第三方设备故障导致总线干扰（Glitch），引发读写错误且无法检测。

2. **地址限制**：I²C协议对设备地址数量有限制，需参考《IPMB I²C地址分配规范》避免冲突。

BMC必须提供一个逻辑主FRU库存设备，用于存储硬件配置信息（如主板序列号、厂商名称等）。

- 必须支持以下IPMI命令：
  
  - `Read FRU Data`（读取FRU数据）
  
  - `Write FRU Data`（写入FRU数据，如更新资产信息）
  
  - `Get FRU Inventory Device Info`（获取FRU设备信息，如存储大小）。
1. **命令概述**
- **功能**：“Read FRU Data Command”（读取现场可更换单元数据命令）用于从FRU（Field Replaceable Unit，现场可更换单元）库存信息区域返回指定的数据。它是一个“低级”的直接接口，用于访问非易失性存储区域。这意味着该接口不会对所访问的数据进行语义解释或格式检查。

- **偏移量说明**：命令中使用的偏移量是“逻辑”偏移量，它可能与用于非易失性存储的物理地址相对应，也可能不对应。例如，FRU信息可以保存在物理地址1234h的FLASH中，但使用此命令访问FRU信息开头时，偏移量仍为0000h。IPMI（Intelligent Platform Management Interface，智能平台管理接口）的FRU设备数据（按照[FRU]格式化的设备）以及处理器和DIMM（Dual In-line Memory Module，双列直插式内存模块）的FRU数据，除非另有说明，通常都从偏移量0000h开始。
2. **消息大小限制相关**
- 虽然偏移量是16位值（允许FRU设备最多达64K字），但读取计数（count to read）、返回计数（count returned）和写入计数字段（count written fields） 仅为8位。这是考虑到消息大小的限制，例如在撰写本文时，IPMB（Intelligent Platform Management Bus，智能平台管理总线）消息总共限制为32字节。
3. **命令数据格式（表34 - ，Read FRU Data Command）**

#### **请求数据（Request Data）**

- **字节1**：FRU设备ID，FFh表示保留。

- **字节2 - 3**：要读取的FRU库存偏移量，分别为低字节（LS Byte）和高字节（MS Byte）。偏移量以设备访问类型的字节或字为单位，在“Get FRU Inventory Area Info”命令中返回。

- **字节4**：读取计数（count to read），计数是基于“1”的（即从1开始计数）。

#### **响应数据（Response Data）**

- **字节1**：完成码（Completion code）
  
  - 通用完成码，加上特定于命令的内容。
  
  - 81h表示FRU设备忙，请求的操作无法完成，因为逻辑FRU设备的实现处于FRU信息暂时不可用的状态。这可能是由于诸如仲裁丢失（如果FRU被实现为共享总线）等情况导致的。如果返回此代码，软件可以选择至少30毫秒后重试该操作。需要注意的是，强烈建议管理控制器包含内置重试机制，因为通用IPMI软件不能依赖于此完成码来自动重试。

- **字节2**：返回计数（count returned），计数是基于“1”的。

- **字节3:2 + N**：请求的数据（Requested data），即从指定FRU库存偏移量读取到的数据。

总体来说，这段内容详细定义了IPMI中用于读取FRU数据的命令格式、相关参数含义以及对消息大小限制等方面的考虑，以便实现对FRU信息的准确读取和交互。

### 体悟：

=>读取外挂eeprom中存储的设备信息，一共可以在IPMB上挂载8个（其中四个地址被基板供应商“预定”）

#### 是什么？

FRU是指可以在现场(如服务器机房等)方**便更换的硬件单元**。FRU实际设备位置是指这个可更换单元在

整个系统(如服务器机箱)中的具体安装位置。例如,某个服务器的硬盘是一个FRU,它可能安装在服务

器机箱的第3个硬盘槽位,这就是它的实际设备位置。BMC需要知道这个位置信息,以便在管理和维护时

准确地定位和操作该FRU。

=>每一个有物理形态可替换的单元都是一个FRU实体，存储FRU的EEPROM也是，有entity ID（提前分配好，实体种类）、entity instance（实例编号，当一种实体有多个时）

#### 怎么读？

一般来说，FRU（如存储设备等）本身存储着设备序列号等信息。当BMC（通过前面提到的I2C通信流程，即Master Write - Read I2C命令，指定从地址A2h（从机地址）和专用总线ID 01（多条总线时的编号））访问FRU相关的存储设备（作为I2C从设备）时：

- 主设备（BMC或其他控制设备）先通过Write操作发送一些命令或地址信息到从设备（FRU存储设备），告诉它需要读取哪个位置（如存储设备中存储序列号的地址）的信息。

- 然后通过Read操作从从设备中读取数据，这样就获取到了设备序列号等FRU信息。例如，如果FRU信息存储在一个EEPROM（电可擦可编程只读存储器，作为I2C从设备）中，EEPROM内部有特定的地址映射来存储序列号等数据，通过上述I2C通信流程就可以读取到这些信息。

#### 为什么？

- **设备识别与管理**：在一个系统（尤其是复杂的计算机系统、服务器系统等）中，需要准确识别每个设备（如FRU设备）。通过查询设备信息（存储在SEEPROM等中的信息，如设备ID、型号、版本等），系统的管理组件（如BMC - 基板管理控制器）可以**知道连接了哪些设备、设备的具体类型和规格等。这有助于进行设备的配置、监控和维护**。例如，**当系统启动或设备插入时，BMC查询设备信息，才能正确识别该设备并为其分配资源、进行参数设置等。**

- **故障诊断与维护**：当系统出现故障或需要进行维护时，设备信息非常关键。知道设备的具体型号、版本等信息，有助于快速定位故障（比如某些版本的设备可能存在已知问题），也有助于更换设备时确保替换的设备与原设备兼容（通过对比设备信息）。例如，如果一个FRU设备（如硬盘模块）出现故障，BMC查询其信息后，可以确定其型号，从而选择合适的备用硬盘模块进行更换。

- **系统配置与优化**：了解设备信息可以帮助系统进行更优的配置。比如根据设备的性能参数（可能存储在设备信息中），系统软件可以调整工作模式、分配任务负载等，以充分发挥设备性能，实现系统的整体优化。
