---
title: CCNA培训学习笔记
date: 2017-06-20 09:59:07
categories: 
  - 学习记录
tags: 
  - Cisco
  - CCNA
  - 学习记录
  - 老坑迁移
---

Serial 串口

### 网络接口
|    以太口   | 网线接口 |         其他       |
|-------------|:--------:|-------------------:|
|  Ethernet   |  以太口  |    10M，半双工口   |
| FastEthernet|快速以太口|100M，双工模式自适应|
|GibitEthernet|          | 1G，双工模式自适应 |

单工：固定只能收只能发
半双工：同一时间内可以收或者发
全双工：同一时间内可以收和发

<!--more-->

### 网络协议

#### ISO组织

#### OSI七层模型

分层的好处

|   模型   |                    用途                  |
|----------|------------------------------------------|
|  应用层  | 制定了各种应用协议（http/https/tenet等）|
|  表示层  | 定义了数据格式，对数据进行加密|
|  会话层  | 建立不同操作系统之间的会话|
|  传输层  | 定义了两种连接方式（可靠连接和不可靠连接）（注意区别TCP/UDP）|
|  网络层  | CLMP（面向无连接的网络服务）不同介质的介入方式|
|数据链路层| 将数据封装成数据帧，对接受到的数据进行校验|
|  物理层  | 定义了各种介质、信号的模式接口的电子电器特性（接口的形状）|

#### 数据封装与数据的解封装

类比网上购物
卖家——货——包裹——快递公司封装成大箱——物流【数据封装】
物流——快递公司——快递员——货——买家【数据的解封装】

<b>上层为下层提供了需求，下层为上层提供了服务</b>

网络层：IP地址
数据链路层：MAC子层（媒体介入控制）LLC子层（数据链路控制）

消息Message
【Message】
【TCP/UDP头】【Message】——传输层 源、目的、端口
【IP包头】【TCP/UDP头】【Message】——网络层 源、目的、IP
【MAC子层】【IP包头】【TCP/UDP头】【Message】——数据链路层 源、目的、MAC
转换为【比特流】——物理层

#### TCP/IP四层模型

|   模型   |         用途          | 传输数据 |
|----------|-----------------------|----------|
|  应用层  | 对应应用层| |
| 端到端层 | 对应传输层（TCP/UDP）| 数据段TPDU |
| 互联网层 | 对应网络层（IP协议）| 报文 |
|网络接口层| 对应数据链路层和物理层| 数据帧、比特流 |

### 线缆

#### 双绞线

A类线序
B类线序（基本用B类）

两边线序相同为直通线，不同线序则为交叉线。
全反线 console线 一般连接设备使用
同层设备使用交叉线，不同层设备使用直通线。

网卡（NIC，网络接口卡）属于网络接口层，既包含物理层的特性，又拥有MAC地址即数据链路层的属性。

#### 以太网标准

|名称|类型|最大传输距离（米）|
|----|----|------------------|
|10Base-T|双绞线|100|
|100Base-TX|双绞线|100|
|1000Base-T|双绞线|100|
|1000Base-LX|单模光纤|5000|
|1000Base-SX|多模光纤|550|

CSMA/CD 带冲突检测的载波监听多路访问
集线器：所有接口处于同一个冲突域和广播域中
交换机：所有接口处于同一个广播域，每个接口都处于一个冲突域中
路由器：每个接口都处于一个冲突域和广播域中

### 交换机

#### 交换机的工作原理

二级交换机和三级交换机
交换机工作在数据链路层

MAC地址表：当pc1想要访问pc2时，交换机会先查找MAC地址表，如果有则直接转发数据帧，如果没有则进行广播查询，如果广播查询到后会进行学习，记录到自己的MAC地址表中。

#### cisco的用户等级

|等级|相关信息|
|----|------------------|
|0|最低等级，5条命令|
|1|20+条命令|
|2-14|能够使用的命令相同，没有太大差别|
|15|最高等级，使用所有命令|

#### 特权模式

```bash
switch>
switch> enable
switch#
```
用户模式切换为特权模式，等级为15

```bash
switch# show running-config (show run)
```
查看当前设备正在运行的配置
配置保存在内存内

```bash
switch# show startup-config
```
查看当前设备正在运行的配置
配置保存在flash内

```bash
switch# copy running-config startup-config
or
switch# write
```
保存配置文件

```bash
switch# reload
```
重启设备

```bash
switch# show mac-address-table
```
查看交换机的MAC地址表

```bash
switch# show ip interface brief
```
查看接口的摘要信息
FastEthernet 0/1 0是模块的信息，1是接口的信息
当后面的信息显示为up up时，表示可以使用。前面up对应物理层状态，后面的up对应数据链路层状态。
Vlan1的状态为administratively为down down，表示管理行为，需要手动开启。

```bash
switch# show clock
switch# clock set 9:20:00 JUN 21 2017
```
特权模式可以修改的唯一参数，修改系统时间
NTP（网络时钟协议）

#### 全局模式

```bash
switch# config terminal (conf t)
switch(config)# 
```
特权模式切换为全局模式，可以对交换机的整体系统参数进行修改

```bash
switch(config)# hostname CCIE-SW1
CCIE-SW1(config)# 
```
修改主机名

```bash
CCIE-SW1(config)# no ip domain lookup
```
关闭交换机的域名查找功能（实验用）
当拼错而导致卡住搜索域名时，组合键shift+ctrl+6回退到特权模式

#### 线路模式

```bash
CCIE-SW1(config)# line console 0
CCIE-SW1(config-line)#
```
全局模式切换为线路模式

```bash
CCIE-SW1(config-line)# no exec-timeout
```
登陆永不超时

```bash
CCIE-SW1(config-line)# logging syn
```
日志同步

```bash
CCIE-SW1(config-line)# password cisco
CCIE-SW1(config-line)# login
```
修改密码并生效

```bash
CCIE-SW1(config)# enable password cisco123
```
修改全局密码，用户模式进入特权模式时输入（明文）

```bash
CCIE-SW1(config)# enable secret cisco1234
```
密文，在明文密文都设置时，密文的优先级更高

优化命令 可以在每次实验时先用这四条命令刷一次配置
```bash
no ip domain lookup
line console 0
no exec-timeout
logging syn
```
tab 补全命令
? 系统提供的帮助
Ctrl+U 将光标前所有命令删除
Ctrl+A 将光标停留在一行的开头
Ctrl+E 将光标停留在一行的末尾
Ctrl+W 删除光标前的一个单词或一个数字

### telnet 远程调试设备

#### console线

#### vty线

虚拟私有终端
必须配置全局enable明文密码

```bash
switch(config)# line vty 0 4
switch(config-line)# password cisco
switch(config-line)# exit
switch(config)# enable password cisco123
```
编号从0到4，一共允许5台设备接入。

当有多台交换机需要维护时，需要对vlan1配置IP地址
```bash
switch(config)# interface vlan 1 
switch(config-if)# ip address 192.168.10.1 255.255.255.0
switch(config-if)# no shutdown
```

当设备进入时，可以使用show users来查看用户

```bash
switch# clear line vty 1
```
将用户踢除

```bash
switch# send * [enter]
[ctrl+Z]
```
给其他用户发送消息

设备互ping时，！表示通，.表示不通

### CDP协议
思科发现协议，构建设备之间的连接信息
用于传递路由条目，涉及到ODR按需路由

show cdp neighbor 查看CDP的邻居
show cdp neighbor detail 查看CDP的邻居的详细信息
clear cdp table 清除cdp表信息

#### CDP表信息
Device ID 邻居设备的主机名
Local Interface 自己连接到邻居的接口
Holdtime 邻居持续时间（默认时间为180s CDP消息每隔60s发送一次 如果180s之后没有收到消息 则刷新表项丢弃邻居）
Capability 可以看Capability Code的信息，R路由器，S交换机，空格为三层交换机
Platform 设备硬件型号
Port ID 邻居连接到自己的接口

### 端口安全
项目中未用到的接口全部shutdown
在正常使用中的端口全部启用端口安全
只允许特定MAC地址的设备能连接到交换机的特定接口，该类MAC地址称为合法MAC
当非法MAC连接时会有三种惩罚：shutdown、protect、restrict

#### 设置合法MAC
```bash
switch(config)# interface f0/1
switch(config-if)# switchport mode access // 指定f0/1为接入口
```

```bash
switch(config-if)# shutdown
switch(config-if)# switchport port-security  // 开启端口安全
switch(config-if)# switchport port-security mac-address 0000.0000.0001 // 静态绑定MAC地址
switch(config-if)# switchport port-security violation shutdown // 设定惩罚模式为shutdown
switch(config-if)# no shutdown
```

将交换机与PC的IP配置到同一个子网下后，交换机会动态学习其他PC的MAC地址到自己的MAC地址表

#### 快速配置合法MAC地址
```bash
switch(config)# interface f0/1
switch(config-if)# switchport port-security mac-address sticky // 把第一个学习到的MAC地址当做合法地址
```

#### 非法MAC惩罚
1. shutdown
当非法MAC连入f0/1时，使用交换机pingPC后，会发现端口关闭(状态down协议down)，查看show interface f0/1时会发现err-disable。这时当合法MAC连入，接口也不会改变状态，需要人为干预。
```bash
switch(config)# interface f0/1
switch(config-if)# shutdown
switch(config-if)# no shutdown
```
2. protect
交换机接口状态不变，但是会静悄悄丢弃你收到的数据帧。

3. restrict
交换机会丢弃收到的数据帧，并且会产生系统日志。

#### vlan
创建vlan10
```bash
switch(config)# vlan 10
switch(config-vlan)# exit
switch(config)# interface f0/1
switch(config-if)# switchport access vlan10
switch(config-if)# switchport mode access
```
到交换机开启trunk（2960默认封装）
```bash
switch(config)# interface f0/1
switch(config-if)# switchport mode trunk
```


### IP地址
相同网段的数据通讯不需要经过网关，而不同网段则需要网关。
IP地址是设备在网络层的标识。
IP地址的地址长度为32比特，分为网络位（网段）和主机位（主机IP），用子网掩码进行区分。
IP地址的总体范围0.0.0.0到255.255.255.255，是点分十进制的格式。

#### 分类

单播IP地址（可配置的）
A类：1.0.0.0 到 126.255.255.255 默认掩码为255.0.0.0 私有地址为1.0.0.0 到 10.255.255.255
B类：128.0.0.0 到 191.255.255.255 默认掩码为255.255.0.0 私有地址为172.16.0.0 到 172.31.255.255
C类：192.0.0.0 到 223.255.255.255 默认掩码为255.255.255.0

组播IP地址（没有掩码，不可配置）
D类：224.0.0.0 到 239.255.255.255 私有地址为239.0.0.0 到 239.255.255.255
E类：240.0.0.0 到 240.255.255.255 以实验为目的


### 路由器

路由器的一个接口占用一个网段
路由器的主要作用是转发不同网段的数据
主机位全0和全1不可用

127.0.0.1 到 127.255.255.255 内部回环地址，测试TCP/IP协议使用。
全1是当前网段的广播地址。
把数据包从一个网段转发到另一个网段的过程就叫做路由。

#### 静态路由
路由表，在路由器中维护的路由条目。路由器会根据路由表进行路径选择。
```bash
[Router0]
router(config)# interface f0/1
router(config-if)# shutdown
router(config-if)# ip address 10.1.1.1
router(config-if)# no shutdown

router(config)# interface f0/0
router(config-if)# shutdown
router(config-if)# ip address 192.168.10.1
router(config-if)# no shutdown

[PC0] ip:192.168.10.2

[Router1]
router(config)# interface f0/1
router(config-if)# shutdown
router(config-if)# ip address 10.1.1.2
router(config-if)# no shutdown

router(config)# interface f0/0
router(config-if)# shutdown
router(config-if)# ip address 192.168.20.1
router(config-if)# no shutdown

[PC1] ip:192.168.20.2
```
配置静态路由表
```bash
router(config)# ip route 192.168.10.0 255.255.255.0 g0/1 // 不同网段的网段信息、掩码和出位置
或
router(config)# ip route 192.168.10.0 255.255.255.0 10.1.1.1 // 不同网段的网段信息、掩码和目标IP
```


#### 动态路由
rip采用跳数作为唯一度量值，经过或者达到一台设备为一跳。
rip采用两种报文格式request和response
rip采用UDB端口号520发送数据
rip V1 采用广播发送数据
rip V2 采用组播发送数据

```bash
router(config)# router rip
router(config-router)# version 2 // 申明版本
router(config-router)# no auto-summary // 关闭自动汇总
router(config-router)# network 192.0.0.0 // 主类宣告网段，必须依据类别和掩码！会根据宣告的地址找到对应的接口
```

```bash
router(config)# ip route
```
查看路由表，C是直连，R是RIP学习。120是rip的管理距离，1代表跳数（度量值）。
不同路由协议收到相同路由条目，比较管理距离。
相同路由协议收到相同路由条目，比较度量值。