# 实验三 Vlan基本配置

##  实验目的

（1）了解常见的Vlan划分方法；

（2）熟悉Vlan的工作原理；

（3）掌握Vlan的配置方法；

（4）掌握Vlan间通信的配置方法。

## 实验内容

（1）在一台交换机上分别创建两个vlan，并为它们分配相应的端口，测试vlan内连通性和vlan间连通性。

（2）在两台交换机上分别创建两个vlan，并为它们分配相应的端口，测试vlan内连通性和vlan间连通性。

（3）通过三层交换机实现Vlan间通信，并测试结果。

（4）抓包分析Vlan间通信时，数据包是如何变化的（从IP地址、MAC地址、MAC帧的格式等方面描述）

## 实验过程

（首先把网络拓扑放前面）

### 1-实验拓扑

1. ![image-20230422205653402](./assets/image-20230422205653402.png)
2. ![image-20230422205852597](./assets/image-20230422205852597.png)
3. ![image-20230422210437123](./assets/image-20230422210437123.png)

### 2-实验步骤

(1) 在一台交换机上分别创建两个vlan，并为它们分配相应的端口，测试vlan内连通性和vlan间连通性。

1. 画出图1的拓扑结构，配置IP地址，并标注

   ![image-20230422215708928](./assets/image-20230422215708928.png)

2. 在交换机上创建两个VLAN(vlan10,vlan20)，将PC0、PC1连接的端口划分到vlan10下，将PC3连接的端口划分到vlan20

   - 创建VLAN

     - 命令行

       ```powershell
       switch#configure  t					!进入全局模式
       switch(config)#vlan 10				!创建vlan 10
       switch(config-vlan)#name vlan10		!命名为vlan10
       Switch(config)#vlan 20				!创建vlan 20
       Switch(config-vlan)# name vlan20 	!命名为vlan20
       Switch#show vlan                 	!显示vlan配置
       ```

     - 图形化

       ![image-20230422125940633](./assets/image-20230422125940633.png)

   - 划分端口

     ```powershell
     Switch(config)# interface range  fa 0/1-2     	!进入接口配置模式
     Switch(config-if)#switchport mode access
     Switch(config-if)#switchport access vlan 10   	!将f0/1-2端口加入vlan 10中
     Switch(config)#interface fastethernet 0/24   	!进入f0/24的接口配置模式
     Switch(config-if)#switchport mode access
     Switch(config-if)#switchport access vlan 20    	!将f0/3端口加入vlan 20中
     ```

     效果图

     ![image-20230422220738591](./assets/image-20230422220738591.png)

     

     

   3. 通过ping命令测试主机间连通性

   - 内连通性

   ![image-20230422221139218](./assets/image-20230422221139218.png)
   - 间连通性

     ![image-20230422221235449](./assets/image-20230422221235449.png)

（2）在两台交换机上分别创建两个vlan，并为它们分配相应的端口，测试vlan内连通性和vlan间连通性。

1. 画出图2的拓扑结构，配置IP地址，并标注

   ![image-20230422221539920](./assets/image-20230422221539920.png)

2. 依次在两台交换机上创建两个VLAN（vlan10、vlan20），并将交换机互连的端口设置为trunk模式

   ```powershell
   Switch>enable
   Switch#configure t
   Switch(config)#interface fastethernet 0/1		!进入接口配置模式
   Switch(config-if)#switchport mode access
   Switch(config-if)#switchport access vlan 10   	!将f0/1端口加入vlan 10中
   Switch(config)#interface fastethernet 0/2   	!进入f0/2的接口配置模式
   Switch(config-if)#switchport mode access
   Switch(config-if)#switchport access vlan 20    	!将f0/2端口加入vlan 20中
   Switch(config)#interface fastethernet 0/13   	!进入f0/3的接口配置模式
   Switch(config-if)#switchport mode trunk
   ```

3. 通过ping命令测试主机间连通性

   - 内连通性
   
     ![image-20230422223215341](./assets/image-20230422223215341.png)
   
   - 间连通性
   
     ![image-20230422223330538](./assets/image-20230422223330538.png)

（3）通过三层交换机实现Vlan间通信，并测试结果。

1. 画出图3的拓扑结构，配置IP地址，并标注

   ![image-20230422224932009](./assets/image-20230422224932009.png)

2. 更改中间两个交换机的设置（两个交换机同样设置）

   ```powershell
   Switch>enable
   Switch#hostname $(name)							!修改主机名程
   Switch#configure t
   Switch(config)#interface fastethernet 0/1		!进入接口配置模式
   Switch(config-if)#switchport mode access
   Switch(config-if)#switchport access vlan 17   	!将f0/1端口加入vlan 10中
   Switch(config)#interface fastethernet 0/2   	!进入f0/2的接口配置模式
   Switch(config-if)#switchport mode access
   Switch(config-if)#switchport access vlan 27    	!将f0/2端口加入vlan 20中
   Switch(config)#interface fastethernet 0/10   	!进入f0/10的接口配置模式
   Switch(config-if)#switchport mode trunk
   ```

3. 设置最上面的交换机

   ```powershell
   Switch(config)#hostname SW1       /*修改主机名为姓名拼音+SW1
   SW1(config)#vlan 17               /*vlan-id为学号后2位+10
   SW1(config-vlan)#exit
   SW1(config)#vlan 27               /*vlan-id为学号后2位+20
   SW1(config-vlan)#exit
   SW1(config)#interface f0/1
   SW1(config-if)#switchport trunk encapsulation dot1q             ！封装模式为dot1q
   SW1(config-if)#switchport mode trunk 
   SW1(config-if)#exit
   SW1(config)#interface f0/2
   SW1(config-if)#switchport trunk encapsulation dot1q             ！封装模式为dot1q
   SW1(config-if)#switchport mode trunk 
   SW1(config)#ip routing
   创建vlan接口vlan17和vlan27，并配置IP地址
   SW1(config)#interface vlan 17   /*vlan-id为学号后2位+10
   SW1(config-if)#ip address 192.168.107.254 255.255.255.0	/*IP地址的第3个字节修改为学号后2位
   SW1(config-if)#no shutdown 
   SW1(config-if)#exit 
   SW1(config)#interface vlan 27  	/*vlan-id为学号后2位+20
   SW1(config-if)#ip address 192.168.207.254 255.255.255.0	/*IP地址的第3个字节修改为学号后2位
   SW1(config-if)#no shutdown 
   ```

4. 在第一台主机通过ping命令测试主机间连通性

   ![image-20230426204801994](./assets/image-20230426204801994.png)

   ![image-20230426204814337](./assets/image-20230426204814337.png)

   ![image-20230426204837960](./assets/image-20230426204837960.png)

## 实验结果分析（实验原理）

#### VLAN间通信

抓包分析VLAN间通信时，数据包是如何变化的（从IP地址、MAC地址、MAC帧的格式等方面描述），即分析VLAN打标签和去标签的过程。

VLAN（Virtual Local Area Network）是一种将单个物理网络划分成多个逻辑网络的技术，可以提供更好的网络管理和安全性。在进行VLAN间通信时，数据包会经历以下变化：

1. VLAN打标签（Tagging）

当一个数据包从一个VLAN传输到另一个VLAN时，需要先将数据包打上标记（Tag），以便接收方知道这个数据包是来自哪个VLAN的。常用的VLAN标记协议有IEEE 802.1Q和Cisco的ISL。在IEEE 802.1Q标准下，一个VLAN标记包含4个字节，其中前两个字节是TPID（Tag Protocol Identifier），指示这是一个VLAN标记数据包；后两个字节是TCI（Tag Control Information），包括VLAN ID、优先级等信息。

2. 数据包头部修改

在打上VLAN标记后，数据包的头部会发生如下变化：

- MAC地址：由于VLAN是基于MAC地址的，所以源MAC地址和目的MAC地址不会改变。但是，在IEEE 802.1Q标准下，一个VLAN标记数据包的目的MAC地址必须是特殊的值0x01-00-5E-00-00-00，因为此时目的地址的低23位会被解释为Multicast Group ID，用于指定目的VLAN。
- IP地址：如果数据包中包含IP地址，则与MAC地址类似，源IP地址和目的IP地址也不会改变。
- MAC帧格式：在打上VLAN标记后，数据包的MAC帧格式会发生变化。具体来说，新增了一个4字节长的VLAN Tag字段，将原来的Type/Length字段替换掉。

3. VLAN去标签（Untagging）

当一个数据包从VLAN传输到非VLAN网络时，需要先将数据包的标记去除，以便接收方能够正确解析数据包。在IEEE 802.1Q标准下，可以使用特殊的VLAN ID值4095表示未打标记的数据包，这种数据包称为native VLAN。去标签的过程与打标签的过程相反，即将VLAN Tag字段删除，恢复原来的Type/Length字段，并把目的MAC地址修改回真实的值。

总的来说，VLAN的打标签和去标签过程是为了将多个逻辑网络划分在同一物理网络中，以提高网络管理和安全性，并且通过对数据包进行加标记和去标记，使得数据包能够在不同的VLAN之间进行传输。

#### Access和Trunk

 

1. 端口类型不同

Access端口是指连接到单个VLAN的端口，只能传输属于该VLAN的数据。通常用于连接主机、服务器或其他单一设备。

Trunk端口是指连接到多个VLAN的端口，可以传输所有VLAN的数据。通常用于连接交换机和路由器等设备，用于实现VLAN间的互联。

2. VLAN标记不同

Access端口上的数据包通常不打上VLAN标记，因为这些数据包都属于相同的VLAN，不需要进行区分。

Trunk端口上的数据包则需要打上VLAN标记，以便接收方知道这个数据包是来自哪个VLAN。

3. 数据包转发方式不同

Access端口只能转发其所属的VLAN中的数据包，而且不会在数据包中添加任何VLAN信息。

Trunk端口可以同时转发多个VLAN中的数据包，并在数据包中打上相应的VLAN标记，以便接收方正确解析数据包。

4. 速率限制不同

Access端口通常没有速率限制，因为它只连接一个单一设备。

Trunk端口则可能需要设置带宽限制和优先级控制，以确保各个VLAN的数据流量得到合理的分配和管理。

综上所述，Access端口和Trunk端口在端口类型、VLAN标记、数据包转发方式和速率限制等方面存在差异，这些差异使得它们适用于不同的网络环境和应用场景。

## 回答问题

（1）**说明划分虚拟局域网的意义。**

1. 安全性：VLAN可以将网络按照不同的用户组或应用场景进行划分，从而控制不同用户或设备之间的通信，有效地防止未经授权的访问和攻击。此外，在VLAN中设置ACL（存取控制列表），可以对数据包进行过滤和验证，进一步提高网络的安全性。
2. 可管理性：VLAN可以将网络分为多个逻辑区域，每个区域可以有自己的管理员，符合公司内部的权限管理和责任分工。同时，在VLAN中可以集中管理相同类型的设备，简化了网络管理的工作量。
3. 灵活性：VLAN可以根据需要进行随时调整，添加或删除设备都不会影响其他VLAN的正常运行。这使得企业或机构能够更加灵活地组织网络结构，快速响应变化的业务需求。

（2）**交换机在配置VLAN前后，冲突域和广播域各有什么不同？**

​	在交换机配置VLAN之前，所有端口都处于同一个冲突域和广播域中。这意味着如果有两台设备同时向网络发送数据包，则数据包会在传输介质上发生碰撞，导致数据丢失或损坏，并可能影响其他设备的通信。

​	然而，当交换机配置了VLAN后，每个VLAN都成为一个独立的冲突域和广播域。这意味着在同一VLAN内的设备可以直接通信，而与不同VLAN内的设备则需要经过路由器才能通信。同时，在每个VLAN中，交换机只会将数据包转发到需要接收该数据包的端口，避免了数据包的广播和冲突。

​	因此，配置VLAN可以有效地隔离网络流量，提高网络的安全性和可靠性，同时也提高了网络的灵活性和可管理性。但是需要注意的是，正确配置VLAN需要考虑网络拓扑结构、物理连接和子网划分等因素，否则可能会导致网络故障或性能问题。