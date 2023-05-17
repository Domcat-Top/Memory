## WireShark

### 1. 概述

- 功能：可以获取HTTP，也能获取HTTPS，但是不能解密HTTPS，所以WireShark看不懂Https中的内容。如果是处理HTTP、HTTPS还是用Fiddler，其他协议比如TCP、UDP，就用WireShark

### 2. 使用方式

1. 选择网卡：
   - WireShark是捕获机器上的某一块网卡的网络包，当你的机器上有多块网卡的时候，你需要选择一个网卡

2. 界面介绍：

   - Display Filter：显示过滤器，用于过滤
   - Packet List Panef：封包列表，显示捕获到的封包，有源地址和目标地址、端口号。颜色不同，代表类型不同
   - Packet Details Pane：封包详细信息，显示封包中的字段
   - Dissector Pane：16进制数据
   - Miscellanous：地址栏，杂项

3. 过滤器：

   - 显示过滤器：用于在捕获的记录中找到所需要的记录

   - 捕获过滤器：用来过滤捕获的封包，以免捕获太多的信息

     > ==在Capture -> Capture Filters中设置，保存过滤==
     >
     > 过滤表达式的规则：
     >
     > - 协议过滤：TCP、UDP等
     > - IP过滤：ip.src = 192.168.1.102 显示源地址为192.168.1.102，ip.dst = 192.168.0.102，目标地址为这个
     > - 端口过滤：tcp.port = 80，端口为80的，tcp.srcport = 80，只显示TCP协议的源端口为80的
     > - HTTP模式过滤：http.request.method = "GET"，只显示HTTP GET方法的
     > - 逻辑运算符为 AND/OR

4. 数据包结构：
   1. 数据包整体概述：Frame
   2. 链路层详细信息，主要是双方的mac地址：Ethernet
   3. 网络层详细信息，主要是双方的IP地址
   4. 传输层的详细信息，主要是双方的端口号













