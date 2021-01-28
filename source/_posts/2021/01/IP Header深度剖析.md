---
title: IP Header深度剖析
date: 2021-01-28 16:08:34
tags: 
  - Ip
---

> 彼岸花花开彼岸，断肠草草断肝肠。

 TCP/IP协议是网络服务中非常重要的协议，那 IP 包的 Header 到底是如何构成的呢？我们一起来剖析剖析。

<!-- more -->

### 基础知识

比特(bit) 是英文 binary digit 的缩写。比特是表示信息的最小单位，是二进制数的一位包含的信息 （1 或 0）所提供的信息量。

字节(Byte) 是计算机信息技术用于计量存储容量的一种计量单位，也表示一些计算机编程语言中的数据类型和语言字符。

**常用换算**

* 1Byte ( 字节 ) = 8bit ( 位，比特 ) 
* 1KB ( Kilobyte，千字节) = 1024B 
* 1MB ( Megabyte，兆字节) = 1024KB 
* 1GB ( Gigabyte，吉字节，千兆) = 1024MB 
* 1TB ( Trillionbyte，万亿字节，太字节) = 1024GB
* 1PB ( Petabyte，千万亿字节，拍字节) = 1024TB 
* 1EB ( Exabyte，百亿亿字节，艾字节) = 1024PB




### TCP/IP 五层模型

```
 +-------------+-------------+-------------------------+
 | Application |  HTTP Packet            |    应用层    |
 +-------------+-------------+-------------------------+
 | Transport   |  TCP Segment            |   传输层     |
 +-------------+-------------+-------------------------+
 | Network     |  IP Packet              |   网络层     |
 +-------------+-------------+-------------------------+
 | Link        |  Frame                  |   链路层     |
 +-------------+-------------+-------------------------+         光纤
 | Physical    |  Bits                   |   物理层     |   ====================> 
 +-------------+-------------+-------------------------+
```

我们主要了解 IP Packet 的 IP Header,图片如下

![IP_PACKAGE](https://s3.ax1x.com/2021/01/28/y98a4S.png)

1. Version（版本）：占 4 比特。标识目前采用的 IP 协议的版本号。一般的值为 0100（IPv4），0110（IPv6）
2. Header Length（IP包头长度）：占 4 比特。作用是为了描述 IP 包头的长度，由于在 IP 包头中有 (IP Options) 变长的可选部分，所以用此字段来说明 IP 包头长度。 因为此处表示的数字的单位是 32 比特(4 字节)，IP包头长度(bit) = 此处表示的数字 * 32(bit), 所以一个 IP 包头最长为 "1111"，即：15*4(字节)=60(字节)。如果 IP 包头不包含可选部分，最小长度为20字节。
3. Type of Service（服务类型）：长度 8 比特。这 8 比特依次表示如 PPP DTRC0。
    * PPP：定义包的优先级，取值越大数据越重要
        - 000 普通 (Routine)
        - 001 优先的 (Priority)
        - 010 立即的发送 (Immediate)
        - 011 闪电式的 (Flash)
        - 100 比闪电还闪电式的 (Flash Override)
        - 101 CRI/TIC/ECP
        - 110 网间控制 (Internetwork Control)
        - 111 网络控制 (Network Control)
    * D 时延: 0:普通 1:延迟尽量小
    * T 吞吐量: 0:普通 1:流量尽量大
    * R 可靠性: 0:普通 1:可靠性尽量大
    * M 传输成本: 0:普通 1:成本尽量小
    * 0 最后一位被保留，恒定为0
4. Total Length（IP包总长）：占 16 比特。 以字节为单位计算的 IP 包的长度 (包括头部和数据)，所以 IP 包最大长度 65535 字节。
5. Identification（标识）：占 16 比特。该字段和 Flags 和 Fragment Offest 字段联合使用，对较大的上层数据包进行分段（fragment）操作。路由器将一个包拆分后，所有拆分开的小包被标记相同的值，以便目的端设备能够区分哪个包属于被拆分开的包的一部分。
6. Flags（标志）：占 3 比特。
    * 第一位不使用 ( 保留位 )。
    * 第二位是 DF（Don't Fragment）位，DF 位设为1时表明路由器不能对该上层数据包分段。如果一个上层数据包无法在不分段的情况下进行转发，则路由器会丢弃该上层数据包并返回一个错误信息。
    * 第三位是 MF（More Fragments）位，当路由器对一个上层数据包分段，则路由器会在除了最后一个分段的IP包的包头中将MF位设为1。
7. Fragment Offset（片偏移）：占 13 比特。表示该 IP 包在该组分片包中位置，接收端靠此来组装还原 IP 包。
8. TTL（生存时间）：占 8 比特。当 IP 包进行传送时，先会对该字段赋予某个特定的值。当 IP 包经过每一个沿途的路由器的时候，每个沿途的路由器会将 IP 包的 TTL 值减少 1。如果 TTL 减少为 0，则该 IP 包会被丢弃。这个字段可以防止由于路由环路而导致 IP 包在网络中不停被转发。
9. Protocol（协议）：占 8 比特。标识了上层所使用的协议。
    * 常用的协议标号
        - 1    ICMP
        - 2    IGMP
        - 6    TCP
        - 17   UDP
        - 88   IGRP
        - 89   OSPF
10. Header Checksum（头部校验和）：占 6 位。用来做 IP 头部的正确性检测，但不包含数据部分。 因为每个路由器要改变 TTL 的值,所以路由器会为每个通过的数据包重新计算这个值。
11. Source Address（源地址）：占 32 比特。标识了这个 IP 包的起源地址。要注意除非使用 NAT，否则整个传输的过程中，这两个地址不会改变。
12. Destination Address（目的地址）：占 32 比特。标识了这个 IP 包的目的地址。要注意除非使用 NAT，否则整个传输的过程中，这两个地址不会改变。
13. IP Options（可选项）：这是一个可变长的字段。该字段属于可选项，主要用于测试，由源设备根据需要改写。
    * 可选项目包含以下内容
        - Loose source routing（松散来源路由）：给出一连串路由器接口的 IP 地址。IP 包必须沿着这些 IP 地址传送，但是允许在相继的两个 IP 地址之间跳过多个路由器。
        - Strict source routing（严格来源路由）：给出一连串路由器接口的IP地址。IP包必须沿着这些IP地址传送，如果下一跳不在IP地址表中则表示发生错误。
        - Record route（路由记录）：当 IP 包离开每个路由器的时候记录路由器的出站接口的 IP 地址。
        - Timestamps（时间戳）：当IP包离开每个路由器的时候记录时间。
14. Padding（填充）：因为 IP 包头长度（Header Length）部分的单位为 32(bit)，所以 IP 包头的长度必须为 32(bit) 的整数倍。因此，在可选项后面，IP 协议会填充若干个 0，以达到 32(bit)的整数倍。 

### 实战分析

wireshark 抓包分析

```
Internet Protocol Version 4, Src: 140.143.52.226, Dst: 192.168.1.95  ===> 互联网 协议 版本 4，源地址：140.143.52.226，目的地址: 192.168.1.95
    0100 .... = Version: 4  ===> IP v4
    .... 0101 = Header Length: 20 bytes (5)  ===> IP首部长度
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)  ===> 差分，服务字段
        0000 00.. = Differentiated Services Codepoint: Default (0)   ===> 区分服务模块
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0) ===> 源端设置以显示源端节点的传输协议
    Total Length: 186  ===> IP数据包的总长度
    Identification: 0x2c40 (11328)  ===> 标识字段
    Flags: 0x40, Don't fragment  ===> 标志字段
        0... .... = Reserved bit: Not set  ===> 保留不用
        .1.. .... = Don't fragment: Set  ===> 分片标志位，1 表示分片，0 表示不分片
        ..0. .... = More fragments: Not set  ===> 最后一片标志位，0 表示最后一片，1 表示不是最后一片
    Fragment Offset: 0  ===> 分段偏移量
    Time to Live: 51  ===> 生存期
    Protocol: TCP (6)  ===> 此包内封装的上层协议为TCP 
    Header Checksum: 0x9785 [validation disabled]  ===> 头部数据的检验和
    [Header checksum status: Unverified]   ===>  头部数据的检验和状态：未验证
    [Good: False]   ===>  数据完整
    [Bad: False]    ===>  数据完整数据不完整
    Source Address: 140.143.52.226  ===> 源地址
    Destination Address: 192.168.1.95  ===> 目的地址
```