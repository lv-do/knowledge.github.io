# 消息中间件

# 概述

## 基于消息中间件的分布式系统的架构

![image-20210401114050362](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401114050362.png)

![](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401114421223.png)

**何谓分布式系统：**

通俗一点：就是一个请求由服务器端的多个服务（服务或者系统）协同处理完成

和单体架构不同的是，单体架构是一个请求发起 jvm调度线程（确切的是 tomcat线程池）分配线程 Thread来处理请求直到释放，而分布式系统是：一个请求时由多个系统共同来协同完成，jvm和环境都可能是独立。如果生活中的比喻的话，单体架构就像建设一个小房子很快就能够搞定，如果你要建设一个鸟巢或者大型的建筑，你就必须是各个环节的协同和分布，这样目的也是项目发展到后期的时候要去部署和思考的问题。我们也不难看出来：分布式架构系统存在的特点和问题如下：

**存在问题：**

1. 学习成本高，技术栈过多
2. 运维成本和服务器成本增高
3. 人员的成本也会增高
4. 项目的负载度也会上升
5. 面临的错误和容错性也会成倍增加
6. 占用的服务器端口和通讯的选择的成本高
7. 安全性的考虑和因素逼迫可能选择 RMI/MQ相关的服务器端通讯

**好处：**

1. 服务系统的独立，占用的服务器资源减少和占用的硬件成本减少，确切的说是：可以合理的分配服务资源，不造成服务器资源的浪费
2. 系统的独立维护和部署，耦合度降低，可插拔性
3. 系统的架构和技术栈的选择可以变的灵活（而不是单纯地选择 java）
4. 弹性的部署，不会造成平台因部署造成的瘫痪和停服的状态

## 消息中间件应用场景

1. 跨系统数据传递
2. 高并发流量削峰
3. 数据的并发和异步处理
4. 大数据分析与传递
5. 分布式事务

比如你有一个数据要进行迁移或者请求并发过多的时候，比如你有10 W的并发请求下订单，我们可以在这些订单入库之前，我们可以把订单请求堆积到消息队列中，让它稳健可靠的入库和执行

![image-20210401114506286](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401114506286.png)

## 常见的消息中间件

- ActiveMQ
- RabbitMQ
- Kafka
- RocketMQ

## 消息中间件的本质及设计

> 它是一种接受数据、接受请求、存储数据、发送数据等功能的技术服务
>
> MQ消息队列：负责数据的传接受，存储和传递，所以性能要高于普通服务和技术


![image-20210401113801483](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401113801483.png)

> 谁来生产消息，存储消息和消费消息呢？

![image-20210401113903697](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401113903697.png)

## 消息中间件的核心组成部分

1. 消息的协议
2. 消息的持久化机制
3. 消息的分发策略
4. 消息的高可用，高可靠
5. 消息的容错机制

## 小结

其实不论选择单体架构还是分布式架构都是项目开发的一个阶段，在什么阶段选择合适的架构方式，而不能盲目追求，最后造成的后果和问题都需要自己买单。但作为一个开发人员学习和探讨新的技术使我们每个程序开发者都应该去保持和思考的问题。当我们没办法去改变社会和世界的时候，我们为了生活和生存那就必须要迎合企业和市场的需求，发挥你的价值和所学的才能，创造价值和实现自我。

# 消息队列协议

> 什么是协议？

![image-20210401115103151](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401115103151.png)

所谓协议是指：

1. 计算机底层操作系统和应用程序通讯时共同遵守的一组约定，只有遵循共同的约定和规范，系统和底层操作系统之间才能相互交流
2. 和一般的网络应用程序的不同它主要负责数据的接受和传递，所以性能比较的高
3. 协议对数据格式和计算机之间交换数据都必须严格遵守规范

> 网络协议的三要素

1. 语法：语法是用户数据与控制信息的结构与格式，以及数据出现的顺序
2. 语义：语义是解释控制信息每个部分的意义，它规定了需要发出何种控制信息，以及完成的动作与做出什么样的响应
3. 时序：时序是对事件发生顺序的详细说明

比如我 MQ发送一个信息，是以什么数据格式发送到队列中，然后每个部分的含义是什么，发送完毕以后的执行的动作，以及消费者消费消息的动作，消费完毕的相应结构和反馈是什么，然后按照对应的执行顺序进行处理。

如果你还是不理解：大家每天都在接触的 `http`请求协议：

1. 语法：`http`规定了请求报文和响应报文的格式。
2. 语义：客户端主动发起请求称之为请求（这是一种定义，同时你发起的是post/get请求）
3. 时序：一个请求对应一个响应（一定先有请求再有响应，这个是时序）

而消息中间件采用的并不是http协议，而常见的消息中间件协议有：`OpenWire`、`AMQP`、`Kafka`、`OpenMessage`协议

> 面试题：为什么消息中间件不直接使用 `http`协议

1. 因为 http请求报文头和响应报文头是比较复杂的，包含了Cookie，数据的加密解密，状态码，响应码等附加的功能，但是对于一个消息而言，我们并不需要这么复杂，也没有这个必要性，它其实就是负责数据传递，存储，分发就行，一定要追求的是高性能。尽量简洁，快速。
2. 大部分情况下 http大部分都是短连接，在实际的交互过程中，一个请求到响应都很有可能会中断，中断以后就不会执行持久化，就会造成请求的丢失。这样就不利于消息中间件的业务场景，因为消息中间件可能是一个长期的获取信息的过程，出现问题和故障要对数据或消息执行持久化等，目的是为了保证消息和数据的高可靠和稳健的运行。

> AMQP协议

**AMQP：（全称：Advanced Message Queuing Protocol）是高级消息队列协议。**

是一个提供统一消息服务的应用层标准高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言等条件的限制。`Erlang`中的实现由 `RabbitMQ`等特性：

1. 分布式事务支持
2. 消息持久化支持
3. 高性能和高可靠的消息处理优势

![image-20210401170156494](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401170156494.png)

> MQTT协议

**MQTT协议（Message Queuing Telemetry Transport）消息队列是 IBM开放的及时通讯协议，物联网系统架构中的重要组成部分**

特点：

1. 轻量
2. 结构简单
3. 传输快，不支持事务
4. 没有持久化设计

应用场景：

1. 适用于计算能力有限
2. 低带宽
3. 网络不稳定的场景

支持者：

![image-20210401170447138](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401170447138.png)

> OpenMessage协议

**最近几年由阿里、雅虎、和滴滴出行、Stremalio等公司共同参与创立的分布式信息中间件、流处理等领域的应用开发标准。**

特点：

1. 结构简单
2. 解析速度快
3. 支持事务和持久化设计

> Kafka协议

**Kafka协议是基于TCP/IP的二进制协议。消息内部是通过长度分割，由一些基本数据类型组成。**

特点：

1. 结构简单
2. 解析速度快
3. 无事务支持
4. 有持久化设计

> 小结

协议：是在 tcp/ip协议基础之上构建的一种约定俗称的规范和机制、它的主要目的可以让客户端（应用程序 Java，go）进行沟通和通讯。并且这种写一下规范必须具有持久性，高可用，高可靠的性能。

# 消息队列持久化

> 持久化

简单来说就是将数据存入磁盘，而不是存在内存中随服务器重启断开而消失，使数据能够永久保存

![image-20210401170851321](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401170851321.png)

> 常见的持久化方式

![image-20210401170915397](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401170915397.png)

# 消息分发策略

> 消息的分发策略

MQ消息 队列有如下几个角色

1. 生产者
2. 存储消息
3. 消费者

那么生产者生成消息以后，MQ进行存储，消费者是如何获取消息的呢？一般获取数据的方式无外乎推（push）或者拉（pull）两种方式，典型的 git就有推拉机制，我们发送的 http请求就是一种典型的拉取数据库数据返回的过程。而消息队列 MQ是一种推送的过程，而这些推机制会使用到很多的业务场景也有很多对应推机制策略

> 场景分析一

![image-20210401171144207](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401171144207.png)

比如我在 APP上下了一个订单，我们的系统和服务很多，我们如何得知这个消息被哪个系统或者哪些服务器或者系统进行消费，那这个时候就需要一个分发的策略。这就需要消费策略。或者称之为消费的方法论

> 场景分析二

![image-20210401171247886](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401171247886.png)

在发送消息的过程中可能会出现异常，或者网络的抖动，故障等等因为造成消息的无法消费，比如用户在下订单，消费 MQ接受，订单系统出现故障，导致用户支付失败，那么这个时候就需要消息中间件就必须支持消息重试机制策略。也就是支持：出现问题和故障的情况下，消息不丢失还可以进行重发

> 消息分发策略的机制和对比

![image-20210401171353397](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401171353397.png)

# 消息队列高可用和高可靠

> 什么是高可用机制

所谓**高可用**：是指产品在规定的条件和规定的时刻或时间内处于可执行规定功能状态的能力

当业务量增加时，请求也过大，一台消息中间件服务器的会触及硬件（CPU，内存，磁盘）的极限，一台消息服务器你已经无法满足业务的需求，所以消息**中间件必须支持集群部署**，来达到高可用的目的

> 集群模式1 - Master-slave主从共享数据的部署方式

![image-20210401171615731](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401171615731.png)



说明：生产者将消费发送到 Master节点，所有的都连接这个消息队列共享这块数据区域，Master节点负责写入，一旦 Master挂掉，slave节点继续服务。从而形成高可用

> 集群模式2 - Master-slave主从同步部署方式

![image-20210401172346134](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401172346134.png)

说明：这种模式写入消息同样在 Master主节点上，但是主节点会同步数据到 slave节点形成副本，和 zookeeper或者 redis主从机制很雷同。这样可以达到负载均衡的效果，如果消费者有多个这样就可以去不同的节点进行消费，因为消息的拷贝和同步会占用很大的带宽和网络资源。在后续的 RabbitMQ中会有使用

> 集群模式3 - 多主集群同步部署模式

![image-20210401172753914](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401172753914.png)

说明：和上面的区别不是特别的大，但是它的写入可以往任意节点去写入

> 集群模式4 - 多主集群转发部署模式

![image-20210401172900781](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401172900781.png)

说明：如果你插入的数据是 broker-1中的，元数据信息会存储数据的相关描述和记录存放的位置（队列）。它会对描述信息也就是元数据信息进行同步，如果消费者在 broker-2中进行消费，发现自己节点没有对应的信息，可以从对应的元数据信息中去查询，然后返回对应的消息信息，场景：比如买火车票或者黄牛买演唱会门票，比如第一个黄牛有顾客说要买的演唱会门票，但是没有但是他回去联系其他的黄牛询问，如果有就返回。

> 集群模式5 Master-slave与 Broker-cluster组合的方案

![image-20210401173026888](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210401173026888.png)

说明：实现多主多从的热备机制来完成消息的高可用以及数据的热备机制，在生产规模达到一定的阶段的时候，这种使用的频率比较高。

> 什么是高可靠机制

所谓高可靠是指：系统可以无故障低持续运行，比如一个系统突然崩溃，报错，异常等等并不影响线上业务的正常运行，出错的几率极低，就称之为：高可靠

在高并发的业务场景中，如果不能保证系统的高可靠，那造成的隐患和损失是非常严重的

如何保证中间件消息的可靠性呢，可以从两个方面考虑：

1. 消息的传输：通过协议来保证系统间数据解析的正确性。
2. 消息的存储区可靠：通过持久化来保证消息的可靠性。

# RabbitMQ

# RabbitMQ入门和安装

> RabbitMQ是一个开源的遵循 AMQP协议实现的基于 Erlang语言编写，支持多种客户端（语言），用于在分布式系统中存储消息，转发消息，具有高可用，高可扩性，易用性等特征

## 下载RabbitMQ

官网：https://www.rabbitmq.com/

> 环境准备：CentOs7.x + Erlang

RabbitMQ是采用 Erlang语言开发的，所以系统环境必须提供 Erlang环境，第一步就是安装 Erlang

### 安装Erlang

> 查看系统版本号

`cat /etc/centos-release	`	#	查看系统版本号

![image-20210402111853534](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402111853534.png)

> 下载对应版本的erlang 

1. 先下载rpm包

   https://github.com/rabbitmq/erlang-rpm/releases/tag/v23.2.1

2. 传输到CentOS

3. rpm包

   rpm -ivh erlang-23.2.1-1.el7.x86_64.rpm

### 安装RabbitMQ

> 下载系统对应版本的RabbitMQ安装包

![image-20210402112558941](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402112558941.png)

> 将下载好的安装包通过Xftp传到CentOS

![image-20210402112431649](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402112431649.png)

> 上传成功

![image-20210402112532073](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402112532073.png)

> 安装RabbitMQ

`rpm -ivh erlang-23.2.1-1.el7.x86_64.rpm`

`yum install rabbitmq-server -y`

> 启动服务

```bash
# 启动服务
systemctl start rabbitmq-server
# 查看服务状态，如图
systemctl status rabbitmq-server.service
# 开机自启动
systemctl enable rabbitmq-server
# 停止服务
systemctl stop rabbitmq-server
```

# RabbitMQ Web管理界面及授权操作

## RabbitMQ管理界面

> 默认情况下，是没有安装Web端的客户端插件，需要安装才可以生效

```bash
rabbitmq-plugins enable rabbitmq_management
```

**说明：rabbitmq有一个默认账号和密码是：`guest`默认情况只能在 localhost本计下访问，所以需要添加一个远程登录的用户**

> 安装完毕以后，重启服务即可

```bash
systemctl restart rabbitmq-server
```

**一定要记住，在对应服务器（阿里云，腾讯云等）的安全组中开放`15672`端口**

> 在浏览器访问

```bahs
# 10.关闭防火墙服务
systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
systemctl stop firewalld   
# 11.访问web管理界面
http://10.15.0.8:15672/
```

![image-20210402155023363](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402155023363.png)

## 授权账号和密码

> 新增用户

`rabbitmqctl add_user admin admin`

> 设置用户分配操作权限

`rabbitmqctl set_user_tags admin administrator`

用户级别：

1. administrator：可以登录控制台、查看所有信息、可以对RabbitMQ进行管理。
2. monitoring：监控者 登录控制台、查看所有信息。
3. policymaker：策略制定者 登录控制台、指定策略。
4. management：普通管理员 登录控制台。

> 为用户添加资源权限

`rabbitmqctl set_permissions -p / admin ".*"".*"".*"`

> 成功页面

![image-20210402160631992](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402160631992.png)

## 小结：

![image-20210402160714831](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402160714831.png)

# RabbitMQ之Docker安装

> 虚拟化容器技术  ——— docker的安装

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-cCZsgWeJ-1615876872951)(C:\Users\VULCAN\AppData\Roaming\Typora\typora-user-images\image-20210315172425057.png)]](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/20210316144242802.png)

> Docker的相关命令

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-q7RPXL0E-1615876872952)(C:\Users\VULCAN\AppData\Roaming\Typora\typora-user-images\image-20210315172808918.png)]](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/20210316144250518.png)

> 安装RabbitMQ

地址： https://www.rabbitmq.com/download.html

![image-20210402161457797](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210402161457797.png)

> 获取rabbit镜像

```bash
docker pull rabbitmq:management
```

> 创建并运行容器

```bash
docker run -id --name=myrabbit -p 15672:15672 rabbitmq:management
--hostname：指定容器主机名称
--name:指定容器名称
-p：将mq端口号映射到本地
或者运行时设置用户和密码
```

# 什么是AMQP？

## AMQP

**AMQP全称：Advanced Message Queuing Protocol（高级消息队列协议）。是应用层协议的一个开发标准，为面向消息的中间件设计**

## AMQP生产者流转过程

![image-20210412144828605](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412144828605.png)

## AMQP消费者流转过程

![image-20210412145006720](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412145006720.png)

# RabbitMQ核心组成部分

## 核心组成部分

![image-20210412145408766](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412145408766.png)

**核心概念：**

- Server：又称Broker，接受客户端的连接，实现AMQP实体服务。安装rabbitmq-server。
- Connection：连接，应用程序与Broker的网络连接TCP/IP三次握手和四次挥手。
- Channel：网络信道，几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道，客户端可以建立各个Channel，每个Channel代表一个会话任务。
- Message：消息，服务与应用程序之间传送的数据，由Properties和body组成，Properties可以对消息进行修饰，比如消息的优先级，延迟等高级特性，Body则是消息体的内容。
- Virtual Host：虚拟地址，用于进行逻辑隔离，最上层的消息路由，一个虚拟主机路由可以有若干个Exchange和Queue，同一个虚拟主机里面不能有相同的名字的Exchange
- Exchange：交换机，接受消息，根据路由键发送消息到绑定的队列。==不具备消息存储的能力==
- Binding：Exchange和Queue之间的虚拟连接，binding中可以保护多个`routing key`。
- Routing key：是一个路由规则，虚拟机可以用它来确定如何路由一个特定消息。
- Queue：队列，也称为Message Queue消息队列，保存消息并将他们转发给消费者。

## RabbitMQ整体架构

![image-20210412150429408](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412150429408.png)

## RabbitMQ运行流程

![image-20210412150456782](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412150456782.png)

## RabbitMQ支持的消息模型

![image-20210413165523522](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413165523522.png)

1. 简单模式 Simple
2. 工作模式 Work
3. 发布订阅模式
4. 路由模式
5. 主题 Topic模式
6. RPC模式
7. 发布者确认模式

# RabbitMQ入门案例  -  Simple 简单模式

## 实现步骤

1. jdk1.8
2. 构建一个 maven工程
3. 导入 rabbitmq的 maven依赖
4. 启动 rabbitmq-server服务
5. 定义生产者
6. 定义消费者
7. 观察消息的在 rabbitmq-server服务中的进程

## 创建一个Maven项目

> 导入相关依赖

> 图解

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-f4gBVwbl-1615906714907)(C:\Users\VULCAN\AppData\Roaming\Typora\typora-user-images\image-20210315191015917.png)]](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/20210316225853904.png)

在上图的模型中，有以下概念：

1. 生产者，也就是要发送消息的程序
2. 消费者：消息的接受者，会一直等待消息到来。
3. 消息队列：图中红色部分。类似一个邮箱，可以缓存消息；生产者向其中投递消息，消费者从其中取出消息。

> 生产者

```java
/**
 * 生产者    简单模式
 */
public class Producer {
    public static void product() throws IOException, TimeoutException {
        //1、创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("101.132.152.182");
        connectionFactory.setPort(5672);
        connectionFactory.setUsername("admin");
        connectionFactory.setPassword("123456");
        connectionFactory.setVirtualHost("/");
        Connection connection = connectionFactory.newConnection("生产者");

        //2、创建通道
        Channel channel = connection.createChannel();

        //3、通过创建交换机，声明队列，绑定关系，路由key，发送消息和接受消息
        /**
         *  参数1：是否持久化，非持久化消息会存盘吗？  会存盘，但是会随着重启服务器而丢失
         *  参数2： 是否独占队列
         *  参数3： 是否自动删除，随着最后一个消费者完毕消息以后是否把队列自动删除
         *  参数4： 携带附属属性
         */
        String queueName = "queue1";
        channel.queueDeclare(queueName, false, false, false, null);
        //4、发送消息给队列queue
        /**
         * 参数1：交换机
         * 参数2：队列、路由key
         * 参数3：消息的状态控制
         * 参数4：消息主题
         */
        //面试题： 可以存在没有交换机的队列吗？  不可能，虽然没有指定交换机  但是一定会存在一个默认的交换机
        String message = "hello";
        channel.basicPublish("", queueName, null, message.getBytes());
        //5、关闭
        channel.close();
        connection.close();

    }
}
```

> 消费者

```java
/**
 * 消费者
 */
public class Consumer {
    public static void consume() throws IOException, TimeoutException {
        //1、创建连接工厂
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("101.132.152.182");
        connectionFactory.setPort(5672);
        connectionFactory.setUsername("admin");
        connectionFactory.setPassword("123456");
        connectionFactory.setVirtualHost("/");
        Connection connection = connectionFactory.newConnection("消费者");
        //2、创建通道
        Channel channel = connection.createChannel();
        //3、接收内容
        channel.basicConsume("queue1", true, new DeliverCallback() {
            @Override
            public void handle(String consumerTag, Delivery message) throws IOException {
                System.out.println("收到消息是" + new String(message.getBody()));

            }
        }, new CancelCallback() {
            @Override
            public void handle(String consumeTag) throws IOException {
                System.out.println("接受失败了" + consumeTag);
            }
        });
    }
}
```

请注意，我们也在这里声明了队列。因为我们可能在发布者之前启动消费者，所以我们希望在使用消息之前确保队列存在。

为什么这里不关闭通道和连接？

如果这样做，程序会先运行，然后关闭所有连接，然后退出！这将是尴尬的，因为我们希望在消费者异步监听消息到达时，该进程保持活动状态。

我们要告诉服务器把队列中的消息传递给我。因为它会异步地向我们发送消息，所以我们以对象的形式提供一个回调，它将缓冲消息，直到我们准备好使用它们。这就是DeliverCallback的作用。



> 测试类

```java
//简单模式测试
@SpringBootTest
public class SimpleTest {
    @Test
    void testProduct() throws IOException, TimeoutException {
        Producer.product();
    }
    @Test
    void testConsume() throws IOException, TimeoutException {
        Consumer.consume();
    }
}
```

> 运行生产者结果

![image-20210412142527455](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412142527455.png)

点击队列名称，Get Message

![image-20210412142616465](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412142616465.png)

> 运行消费者结果

![image-20210412142944532](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412142944532.png)

![image-20210412142658252](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412142658252.png)

# RabbitMQ入门案例 - fanout 模式

## 发布订阅模式

> 图解

![image-20210412151306003](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210412151306003.png)

**发布订阅模式的具体实现**

- web操作查看视频
- 类型：fanout
- 特点：Fanout - 发布订阅模式，是一种广播机制，它是没有路由key的模式。

> 获取连接工具类

```java
/**
 * 获取rabbitmq连接工具类
 */
public class ConnectionUtil {
    public static Connection getConnection() throws IOException, TimeoutException {
        //1、创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("101.132.152.182");
        factory.setPort(5672);
        factory.setUsername("admin");
        factory.setPassword("123456");
        factory.setVirtualHost("/");
        return factory.newConnection();
    } 
}
```

> 生产者

```java
/**
 * 发布订阅模式
 *
 * <p>
 * 说明：可实现一条消息被多个消费者消费
 * <p>
 * a. 一个生产者，多个消费者；
 * b. 每一个消费者都有自己的消息队列；
 * c. 生产者没有把消息发送到队列，而是发送到交换器exchange上；
 * d. 每个队列都需要绑定到交换机上；
 * e. 生产者生产的消息先经过交换机然后到达队列，一个消息可以被多个消费者消费；
 */
public class Producer {
    private static final String PUBLISH_SUBSCRIBE_EXCHANGE_NAME = "publish_subscribe_exchange_fanout";
    //类型：分发
    private static final String PUBLISH_SUBSCRIBE_EXCHANGE_TYPE = "fanout";
    public static void product() {
        Connection connection = null;
        //从链接中获取Channel通道对象
        Channel channel = null;
        try {
            //1.获取MQ连接
            connection = ConnectionUtil.getConnection();
            //从连接中获取Channel对象
            channel = connection.createChannel();
            //创建交换机对象publish_subscribe_exchange_fanout
            channel.exchangeDeclare(PUBLISH_SUBSCRIBE_EXCHANGE_NAME,PUBLISH_SUBSCRIBE_EXCHANGE_TYPE);
            //发送消息到交换机exchange上
            String message = "hello world";
            channel.basicPublish(PUBLISH_SUBSCRIBE_EXCHANGE_NAME, "", null, message.getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        } finally {
            if (null != channel) {
                try {
                    channel.close();
                } catch (IOException | TimeoutException e) {
                    e.printStackTrace();
                }
            }
            if (null != connection) {
                try {
                    connection.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

> 消费者1

```java
/**
 * 发布订阅模式
 */
public class Consumer {
    public static void consume() throws IOException, TimeoutException {

        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //创建队列
        channel.queueDeclare("fanout_queue1",false,false,false,null);
        //将队列绑定到交换机上
        channel.queueBind("fanout_queue1","publish_subscribe_exchange_fanout", "");
        channel.basicQos(1);

        //创建消费者对象
        DefaultConsumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                //消息消费者获取消息
                String message = new String(body, StandardCharsets.UTF_8);
                System.out.println("【CustomConsumer01】receive message: " + message);
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    //消费完一条消息需要自动发送确认消息给MQ
                    channel.basicAck(envelope.getDeliveryTag(), false);
                }
            }
        };
        //使用公平分发必须关闭自动应答
        boolean autoAck = false;
        //监听消息队列
        channel.basicConsume("fanout_queue1", autoAck, consumer);

    }
}
```

> 消费者2

```java
/**
 * 发布订阅模式
 */
public class Consumer2 {
    public static void consume() throws IOException, TimeoutException {

        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //创建队列
        channel.queueDeclare("fanout_queue2",false,false,false,null);
        //将队列绑定到交换机上
        channel.queueBind("fanout_queue2","publish_subscribe_exchange_fanout", "");
        channel.basicQos(1);

        //创建消费者对象
        DefaultConsumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                //消息消费者获取消息
                String message = new String(body, StandardCharsets.UTF_8);
                System.out.println("【CustomConsumer02】receive message: " + message);
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    //消费完一条消息需要自动发送确认消息给MQ
                    channel.basicAck(envelope.getDeliveryTag(), false);
                }
            }
        };
        //使用公平分发必须关闭自动应答
        boolean autoAck = false;
        //监听消息队列
        channel.basicConsume("fanout_queue2", autoAck, consumer);

    }
}
```

> 测试

```java
@Test
void testProduct(){
    Producer.product();
}
```

```java
 public static void main(String[] args) throws IOException, TimeoutException {
        Consumer.consume();
        Consumer2.consume();
    }
```

> 测试结果

​		交换机上发布消息，两个消费者消费同一条消息

![image-20210413115504294](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413115504294.png)

![image-20210413140001934](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413140001934.png)

由此可见，一条消息同时被两个消费者同时消费。

> 交换机信息

![image-20210413140206548](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413140206548.png)

> 交换机绑定信息

![image-20210413140225982](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413140225982.png)

**重点：**

- 一个生产者，多个消费者
- 每一个消费者都有自己的消息队列，分别绑定在不同的队列上
- 生产者没有把消息发送到队列，而是发送到交换机exchange上
- 每个队列又需要绑定到交换机上
- 生产者生产的消息先经过交换机然后到达队列，一个消息可以被多个消费者消费
- 如果消息发送没有队列绑定的交换机时，消息将会丢失，因为交换机没有存储消息的能力，只有队列才有存储消息的能力

# RabbitMQ入门案例 - Direct模式 路由模式（Routing）

## 直接交换模式

生产者和消费者，具有相同的交换机名称（Exchange）、交换机类型和相同的密匙（routingKey），那么消费者即可成功获取到消息。
（PS：相对比只要交换机名称即可接收到消息的广播模式（fanout），direct模式在其基础上，多加了一层密码限制（routingKey）。）

> RabbitMQ消息模型的**核心思想（core idea）**: 生产者会把消息发送给RabbitMQ的交换中心（Exchange），Exchange的一侧是生产者，另一侧则是一个或多个队列，由Exchange决定一条消息的生命周期–发送给某些队列，或者直接丢弃掉。

**direct: 通过routingKey和exchange决定的那个唯一的queue可以接收消息**

**原理：消息发布端分别发送INFO，WARNING，ERROR类型的消息，C1 C2 C3订阅了不同类型的消息**

![image-20210413161433460](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413161433460.png)

![image-20210413141632533](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413141632533.png)

> 生产者

```java
/**
 * 直接交换模式
 */
public class Producer {
    public static void product(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //创建direct类型的交换机
            channel.exchangeDeclare("publish_subscribe_exchange_direct", BuiltinExchangeType.DIRECT);
            String message = "hello direct模式";
            //消息发布
            channel.basicPublish("publish_subscribe_exchange_direct", "info", null, message.getBytes());
            System.out.println("message发送成功");
            channel.close();
            connection.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 消费者

```java
/**
 * 直接交换模式
 */
public class Consumer {
    public static void consume(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //创建队列
            channel.queueDeclare("direct_queue", false, false, false, null);
            //队列与交换机绑定（参数为：队列名称；交换机名称；密匙-routingKey）
            channel.queueBind("direct_queue", "publish_subscribe_exchange_direct", "info");
            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel){
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    try {
                        //消息消费者获取消息
                        String message = new String(body, StandardCharsets.UTF_8);
                        System.out.println("receive message: " + message);
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        //消费完一条消息需要自动发送确认消息给MQ
                        channel.basicAck(envelope.getDeliveryTag(), false);
                    }
                }
            };
            //声明队列中被消费掉的消息（参数为：队列名称；消息是否自动确认;consumer主体）
            channel.basicConsume("direct_queue",true,consumer);
            //这里不能关闭连接，调用了消费方法后，消费者会一直连接着rabbitMQ等待消费
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 测试生产者

```
@Test
void testProduct(){
    Producer.product();
}
```

> 结果

![](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413143324077.png)

没有绑定队列

![image-20210413143427300](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413143427300.png)

> 测试消费者

首先启动消费者

```java
public static void main(String[] args) {
    Consumer.consume();
}
```

使用错误的routingKey发布消息，结果

![image-20210413143652745](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413143652745.png)

![image-20210413143725760](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413143725760.png)

使用info发布消息

![image-20210413143747640](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413143747640.png)

![image-20210413143756226](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413143756226.png)

**由此可知，只有消费者的routingKey为：`info`，与生产者一致。消息接收成功**
**消费者的routingKey为：其他，与生产者不一致。消息接收失败**

# RabbitMQ入门案例 - Topic模式

## 主题模式

> 图解

![image-20210413144504636](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413144504636.png)

类似于正则表达式匹配的一种模式。主要使用#、*进行匹配。

> 生产者

```java
/**
 * 主题模式
 *
 * 说明：
 * #: 代表一个或者多个
 * *: 代表一个
 *
 * 举例：
 * 比如发送消息的时候指定了routing key为news.insert,
 * 如果消费者指定binding key 为news.* 或者news.#都能接收到该消息;
 */
public class Producer {
    //指定交换机名称
    private static final String EXCHANGE_NAME = "exchange_topic";
    //指定routing key
    private static final String EXCHANGE_ROUTE_KEY = "news.insert";

    public static void product(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //创建交换机对象
            channel.exchangeDeclare(EXCHANGE_NAME, ExchangeTypes.TOPIC);
            //发送消息到交换机上
            String message = "topic模式";
            channel.basicPublish(EXCHANGE_NAME, EXCHANGE_ROUTE_KEY, null, message.getBytes());
            channel.close();
            connection.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 消费者1

```java
public class Consumer {
    private static final String QUEUE_NAME = "topic_queue_name1";
    private static final String EXCHANGE_NAME = "exchange_topic";
    //binding key
    private static final String EXCHANGE_ROUTE_KEY = "news.#";

    public static void consume() {
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            //将队列绑定到交换机上,并且指定routing_key
            channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, EXCHANGE_ROUTE_KEY);

            channel.basicQos(1);
            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    //消息消费者获取消息
                    String message = new String(body, StandardCharsets.UTF_8);
                    System.out.println("【CustomConsumer01】receive message: " + message);
                    try {
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        //消费完一条消息需要自动发送确认消息给MQ
                        channel.basicAck(envelope.getDeliveryTag(), false);
                    }
                }
            };

            //使用公平分发必须关闭自动应答
            boolean autoAck = false;
            //监听消息队列
            channel.basicConsume(QUEUE_NAME, autoAck, consumer);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 消费者2

```java
public class Consumer2 {
    private static final String QUEUE_NAME = "topic_queue_name2";
    private static final String EXCHANGE_NAME = "exchange_topic";
    //binding key
    private static final String EXCHANGE_ROUTE_KEY = "news.insert";

    public static void consume() {
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            //将队列绑定到交换机上,并且指定routing_key
            channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, EXCHANGE_ROUTE_KEY);

            channel.basicQos(1);
            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    //消息消费者获取消息
                    String message = new String(body, StandardCharsets.UTF_8);
                    System.out.println("【CustomConsumer02】receive message: " + message);
                    try {
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        //消费完一条消息需要自动发送确认消息给MQ
                        channel.basicAck(envelope.getDeliveryTag(), false);
                    }
                }
            };

            //使用公平分发必须关闭自动应答
            boolean autoAck = false;
            //监听消息队列
            channel.basicConsume(QUEUE_NAME, autoAck, consumer);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 消费者测试

```java
@Test
void testProduct(){
    Producer.product();
}
```

![image-20210413150222506](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413150222506.png)

> 消费者测试

```java
public static void main(String[] args) {
    Consumer.consume();
    Consumer2.consume();
}
```

启动

![image-20210413150632072](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413150632072.png)

交换机绑定信息

![image-20210413150653437](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413150653437.png)

发布消息`news.insert`

![image-20210413150751704](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413150751704.png)

控制台结果，两个消费者均能收到

![image-20210413150824142](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413150824142.png)

发布消息`news.other`

![image-20210413150852730](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413150852730.png)

控制台结果,只有消费者1能收到

![image-20210413151019760](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413151019760.png)

**由此可知，生产者发送消息绑定的routing key 为news.insert；消费者1监听的队列和交换器binding key 为news.#；消费者2监听的队列和交换器bindingkey为news.insert,很显然，两个消费者都将接收到该消息。**

# RabbitMQ入门案例 - Work模式（工作队列模式）

> 图解

![image-20210413151935297](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413151935297.png)

多个消费者绑定到同一个队列上，一条消息只能被一个消费者进行消费。工作队列有**轮询分发和公平分发**两种模式。

## Work模式轮询模式

> 生产者

```java
/**
 * 工作队列 - 消息生产者
 * 消费者1与消费者2处理的消息是均分的，而且消息是轮训分发的(轮询分发 round-robin)
 */
public class Producer {
    private static final String WORK_QUEUE_NAME = "MQ_WORK_QUEUE";
    private static final String WORK_QUEUE_MESSAGE = "hello world!! ------> ";
    public static void product(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //创建Queue队列
            channel.queueDeclare(WORK_QUEUE_NAME, false, false, false, null);
            //发送10条消息到工作队列
            for (int i = 1; i <= 10; i++) {
                StringBuilder msg = new StringBuilder(WORK_QUEUE_MESSAGE).append(i);
                //发送消息
                channel.basicPublish("", WORK_QUEUE_NAME, null, msg.toString().getBytes());
            }
            channel.close();
            connection.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }

    }
}
```

> 消费者1

```java
public class Consumer {
    private static final String WORK_QUEUE_NAME = "MQ_WORK_QUEUE";
    public static void consume(){
        Connection connection = null;
        try {
            connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //声明queue队列
            channel.queueDeclare(WORK_QUEUE_NAME, false, false, false, null);
            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    //消息消费者获取消息
                    String message = new String(body, StandardCharsets.UTF_8);
                    System.out.println("【CustomConsumer01】receive message: " + message);
                    try {
                        //模拟延迟
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            };

            //监听消息队列
            channel.basicConsume(WORK_QUEUE_NAME, true, consumer);

        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 消费者2

```java
public class Consumer2 {
    private static final String WORK_QUEUE_NAME = "MQ_WORK_QUEUE";
    public static void consume(){
        Connection connection = null;
        try {
            connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //声明queue队列
            channel.queueDeclare(WORK_QUEUE_NAME, false, false, false, null);
            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    //消息消费者获取消息
                    String message = new String(body, StandardCharsets.UTF_8);
                    System.out.println("【CustomConsumer02】receive message: " + message);
                    try {
                        //模拟延迟
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            };

            //监听消息队列
            channel.basicConsume(WORK_QUEUE_NAME, true, consumer);

        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 测试

先运行消费者

```java
public static void main(String[] args) {
    Consumer.consume();
    Consumer2.consume();
}
```

在运行生产者

```java
@Test
void testProduct(){
    Producer.product();
}
```

测试结果

![](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413155211593.png)

![image-20210413155451450](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413155451450.png)

由上面图可见，消费者1和消费者2处理的消息是均分的(消费的消息条数一样)，而且消息是轮询分发的，也就是说同一个消息只能被一个消费者消费。上面的消费者1和消费者2处理消息的效率不同，但是最后接收到的消息还是一样多，如果需要让工作效率高的消费者消费更多的消息，那么可以使用公平分发，下面介绍一下工作队列的**公平分发模式(能者多劳)**。
## Work模式公平分发模式

> 生产者

```java
/**
 * @Description: 工作队列 - 消息生产者 (公平分发方式Fair dispatch)
 * <p>
 * 说明：
 * 1. 生产者、消费者指定：channel.basicQos(1);
 * 2. 消费者消费完消息自动发送确认消息：channel.basicAck(envelope.getDeliveryTag(), false);
 * 3. 消费者必须关闭自动应答：autoAck = false;
 * 4. 一般消费者如果处理消息的时间较短(效率较高)，那么它处理的消息会比较多一些;
 */
public class Producer {
    private static final String WORK_QUEUE_NAME = "MQ_WORK_QUEUE";
    private static final String WORK_QUEUE_MESSAGE = "hello world!! ------> ";
    public static void product(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //创建Queue队列
            channel.queueDeclare(WORK_QUEUE_NAME, false, false, false, null);

            //每个消费者发送确认消息之前，消息队列不发送下一个消息到消费者(同一时刻服务器只会发送一条消息给消费者),消费者端发送了ack后才会接收下一个消息。
            channel.basicQos(1);

            //发送10条消息到工作队列
            for (int i = 1; i <= 10; i++) {
                StringBuilder msg = new StringBuilder(WORK_QUEUE_MESSAGE).append(i);
                //发送消息
                channel.basicPublish("", WORK_QUEUE_NAME, null, msg.toString().getBytes());
            }

            channel.close();
            connection.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }

}
```

> 消费者1

```java
public class Consumer {
    private static final String WORK_QUEUE_NAME = "MQ_WORK_QUEUE";
    public static void consume(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //声明queue队列
            channel.queueDeclare(WORK_QUEUE_NAME, false, false, false, null);

            channel.basicQos(1);

            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    //消息消费者获取消息
                    String message = new String(body, StandardCharsets.UTF_8);
                    System.out.println("【CustomConsumer01】receive message: " + message);
                    try {
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        //消费完一条消息需要自动发送确认消息给MQ
                        channel.basicAck(envelope.getDeliveryTag(), false);
                    }
                }
            };

            //使用公平分发必须关闭自动应答(autoAck：true自动返回结果，false手动返回)
            boolean autoAck = false;
            //监听消息队列
            channel.basicConsume(WORK_QUEUE_NAME, autoAck, consumer);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 消费者2

```java
public class Consumer2 {
    private static final String WORK_QUEUE_NAME = "MQ_WORK_QUEUE";
    public static void consume(){
        try {
            Connection connection = ConnectionUtil.getConnection();
            Channel channel = connection.createChannel();
            //声明queue队列
            channel.queueDeclare(WORK_QUEUE_NAME, false, false, false, null);

            channel.basicQos(1);

            //创建消费者对象
            DefaultConsumer consumer = new DefaultConsumer(channel) {
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                    //消息消费者获取消息
                    String message = new String(body, StandardCharsets.UTF_8);
                    System.out.println("【CustomConsumer02】receive message: " + message);
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        //消费完一条消息需要自动发送确认消息给MQ
                        channel.basicAck(envelope.getDeliveryTag(), false);
                    }
                }
            };

            //使用公平分发必须关闭自动应答(autoAck：true自动返回结果，false手动返回)
            boolean autoAck = false;
            //监听消息队列
            channel.basicConsume(WORK_QUEUE_NAME, autoAck, consumer);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

> 测试

测试代码与上面一致。

先启动消费者，后启动生产者，查看结果

![image-20210413161335013](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413161335013.png)

由此可见，消费者2的效率相对较高，所以消费者2消费消息比消费者1多一些，这样就可以充分发挥消费者处理消息的能力。

**重点：**

1. 生产者、消费者指定：channel.basicQos(1);
2. 消费者消费完消息自动发送确认消息：channel.basicAck(envelope.getDeliveryTag(), false);
3. 消费者必须关闭自动应答：autoAck = false;
4. 注意Consumer1休眠2000ms，Consumer2休眠1000ms。
5. 一般消费者如果处理消息的时间较短(效率较高)，那么它处理的消息会比较多一些;

# RabbitMQ入门案例 - RPC模式

## RPC模式

> 图解

![image-20210413161958333](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413161958333.png)

RPC即客户端远程调用服务端的方法 ，使用MQ可以实现RPC的异步调用，基于Direct交换机实现，流程如下：
1、客户端即是生产者就是消费者，向RPC请求队列发送RPC调用消息，同时监听RPC响应队列。
2、服务端监听RPC请求队列的消息，收到消息后执行服务端的方法，得到方法返回的结果
3、服务端将RPC方法的结果发送到RPC响应队列
4、客户端（RPC调用方）监听RPC响应队列，接收到RPC调用结果。
[RPC案例地址](https://www.cnblogs.com/zpKang/p/13599813.html)

> 案例参考地址

https://blog.csdn.net/Weixiaohuai/article/details/93494018?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-5&spm=1001.2101.3001.4242

# RabbitMQ使用场景

## 解耦、削峰、异步

> 同步异步问题

串行方式：将订单信息写入数据库成功后，发送注册邮件，在发送注册短信。以上三个任务全部完成后，返回给客户端。

![image-20210413164701347](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413164701347.png)

```java
public void makeOrder(){
    //1.发送订单
    //2.发送短信服务
    //3.发送email服务
    //4.发送app服务
}
```

> 并行方式   异步线程池

并行方式：将订单信息写入数据库成功后，发送注册邮件的同时，发送注册短信。以上三个任务完成后，返回给客户端。与串行的差别是，并行的方式可以提高处理的时间。

![image-20210413164712182](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413164712182.png)

```java
public void test(){
    //异步
    theadpool.submit(new Callable<Object>{
        //1.发送短信服务
    })
    //异步
    theadpool.submit(new Callable<Object>{
        //2.
    })
    //异步
    theadpool.submit(new Callable<Object>{
        //3.
    })
    //异步
    theadpool.submit(new Callable<Object>{
        //4.
    })
}
```

存在问题

1. 耦合度高
2. 需要自己写线程池自己维护成本太高
3. 出现了消息可能会丢失，需要你自己做消息补偿
4. 如何保证消息的可靠性你自己写
5. 如果服务器承载不了，你需要自己去写高可用

> 异步消息队列的方式

![image-20210413164722287](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210413164722287.png)

好处：

1. 完全解耦，用 MQ建立桥接
2. 有独立的线程池和运行模型
3. 出现了消息可能会丢失，MQ有持久化功能
4. 如何保证消息的可靠性，死信队列和消息转移等
5. 如果服务器承载不了，你需要自己去写高可用，HA镜像模型高可用

`按照以上约定，用户的响应时间相当于是订单信息写入数据库的时间，也就是50ms。注册邮件，发送短信写入消息队列后，直接返回，因为写入消息队列的速度很快，基本可以忽略，因此用户的响应时间可能是50ms。因此架构改变后，系统的吞吐量提高到每秒20QPS。比串行提高了三倍，比并行提高了两倍。`

## 高内聚 低耦合

![img](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/fc06cabe3965d8328625c0dc540e1d87.png)

![img](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/8189586499c51f8af5833664bd752a5f.png)

# Demo代码地址

https://gitee.com/liu_shaoxiong/rabbit-mq-spring-boot-demo



------

# SpringBoot整合案例 - fanout

## 创建SpringBoot项目

![image-20210414110355835](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414110355835.png)

## 生产者 producer

![image-20210414111007341](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414111007341.png)

### maven依赖 Maven Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

### 配置文件 application.yml

```yaml
server:
  port: 8080

# 配置rabbitmq服务
spring:
  rabbitmq:
    username: admin
    password: 123456
    virtual-host: /
    host: laoliu0402.top
    port: 5672
```

### OrderService.java

```java
@Service
public class OrderService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    //模拟用户下单
    public void makeOrder(String userId,String productId,int num){
        //根据商品id查询库存是否足够

        //保存订单
        String orderId = UUID.randomUUID().toString();
        System.out.println("订单生产成功：" + orderId);
        //通过MQ来完成消息的分发
        String exchangeName = "fanout_order_exchange";
        String routingKey = "";
        rabbitTemplate.convertAndSend(exchangeName, routingKey, orderId);

    }
}
```

> 这里可以写个接口调用OrderService

### OrderController.java

```java
@RestController
public class OrderController {
    @Autowired
    private OrderService orderService;
    @GetMapping("/test")
    public void test(){
        orderService.makeOrder("1", "1", 1);
    }
}
```

## 消费者 Consumer

![image-20210414111104446](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414111104446.png)

### Maven 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

### 配置文件

```yaml
server:
  port: 8081

# 配置rabbitmq服务
spring:
  rabbitmq:
    username: admin
    password: 123456
    virtual-host: /
    host: laoliu0402.top
    port: 5672
```

>  声明注册fanout模式的交换机,并配置交换机和队列之间的绑定关系

### RabbitMQConfig.java

```java
@Configuration
public class RabbitMQConfig {
    //声明注册fanout模式的交换机
    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange("fanout_order_exchange",true,false);
    }

    //声明队列
    @Bean
    public Queue smsQueue(){
        return new Queue("sms.fanout.queue", true);
    }
    @Bean
    public Queue duanxinQueue(){
        return new Queue("duanxin.fanout.queue", true);
    }
    @Bean
    public Queue emailQueue(){
        return new Queue("email.fanout.queue", true);
    }

    //完成队列与交换机之间绑定关系
    @Bean
    public Binding smsBinding(){
        return BindingBuilder.bind(smsQueue()).to(fanoutExchange());
    }
    @Bean
    public Binding duanxinBinding(){
        return BindingBuilder.bind(duanxinQueue()).to(fanoutExchange());
    }
    @Bean
    public Binding emailBinding(){
        return BindingBuilder.bind(emailQueue()).to(fanoutExchange());
    }
}
```

> 三个消费者

### FanoutSmsConsumer.java

```java
@Component
public class FanoutSmsConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"sms.fanout.queue"})
    public void receiveMessage(String message){
        System.out.println("sms接受到了订单信息是："+message);
    }
}
```

### FanoutDuanxinConsumer.java

```java
@Component
public class FanoutDuanxinConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"duanxin.fanout.queue"})
    public void receiveMessage(String message){
        System.out.println("duanxin接受到了订单信息是："+message);
    }
}
```

### FanoutEmailConsumer.java

```java
@Component
public class FanoutEmailConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"email.fanout.queue"})
    public void receiveMessage(String message){
        System.out.println("email接受到了订单信息是："+message);
    }
}
```

## 测试

> 启动两个工程，用Postman调用 localhost:8080/test ，查看控制台结果。

![image-20210414111805676](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414111805676.png)



![image-20210414111833422](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414111833422.png)

![image-20210414111849665](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414111849665.png)

# SpringBoot整合案例 - Direct

> 注意如何和上面的工程放在一个项目当中，需要把fanout当中的注解注释，防止冲突

## 生产者

### OrderService.java

```java
@Service
public class OrderService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    //模拟用户下单
    public void makeOrder(String userId,String productId,int num){
        //根据商品id查询库存是否足够

        //保存订单
        String orderId = UUID.randomUUID().toString();
        System.out.println("订单生产成功：" + orderId);
        //通过MQ来完成消息的分发
        String exchangeName = "direct_order_exchange";
        rabbitTemplate.convertAndSend(exchangeName, "sms", orderId);
        rabbitTemplate.convertAndSend(exchangeName, "email", orderId);

    }
}
```

## 消费者

### RabbitMQConfig.java

```java
@Configuration
public class RabbitMQConfig {
    //声明注册direct模式的交换机
    @Bean
    public DirectExchange directExchange(){
        return new DirectExchange("direct_order_exchange",true,false);
    }

    //声明队列
    @Bean
    public Queue smsQueue(){
        return new Queue("sms.direct.queue", true);
    }
    @Bean
    public Queue duanxinQueue(){
        return new Queue("duanxin.direct.queue", true);
    }
    @Bean
    public Queue emailQueue(){
        return new Queue("email.direct.queue", true);
    }

    //完成队列与交换机之间绑定关系
    @Bean
    public Binding smsBinding(){
        return BindingBuilder.bind(smsQueue()).to(directExchange()).with("sms");
    }
    @Bean
    public Binding duanxinBinding(){
        return BindingBuilder.bind(duanxinQueue()).to(directExchange()).with("duanxin");
    }
    @Bean
    public Binding emailBinding(){
        return BindingBuilder.bind(emailQueue()).to(directExchange()).with("email");
    }
}
```

### DirectSmsConsumer.java

```java
@Component
public class DirectSmsConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"sms.direct.queue"})
    public void receiveMessage(String message){
        System.out.println("sms接受到了订单信息是："+message);
    }
}
```

### DirectEmailConsumer.java

```java
@Component
public class DirectEmailConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"email.direct.queue"})
    public void receiveMessage(String message){
        System.out.println("email接受到了订单信息是："+message);
    }
}
```

### DirectDuanxinConsumer.java

```java
@Component
public class DirectDuanxinConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"duanxin.direct.queue"})
    public void receiveMessage(String message){
        System.out.println("duanxin接受到了订单信息是："+message);
    }
}
```

## 测试

> 启动生产者消费者

![image-20210414134046679](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414134046679.png)

> 请求 localhost:8080/direct，查看结果

生产者发布消息成功：

![image-20210414134135545](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414134135545.png)

消费者获得到指定的routingKey消息：

![image-20210414134202082](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414134202082.png)

> 发布一条专属于SMS的消息测试一下：

![image-20210414134300827](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414134300827.png)

结果：

![image-20210414134315591](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414134315591.png)

# SpringBoot整合案例 - Topic

## 生产者

### OrderService.java

```java
@Service
public class OrderService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    //模拟用户下单
    public void makeOrder(String userId,String productId,int num){
        //根据商品id查询库存是否足够

        //保存订单
        String orderId = UUID.randomUUID().toString();
        System.out.println("订单生产成功：" + orderId);
        //通过MQ来完成消息的分发
        String exchangeName = "topic_order_exchange";
        rabbitTemplate.convertAndSend(exchangeName, "sms.message1", orderId);
        rabbitTemplate.convertAndSend(exchangeName, "sms.message2", orderId);
        rabbitTemplate.convertAndSend(exchangeName, "duanxin.message3", orderId);
        rabbitTemplate.convertAndSend(exchangeName, "email.message4", orderId);
        rabbitTemplate.convertAndSend(exchangeName, "email.message5", orderId);
    }
}
```

## 消费者

### RabbitMQConfig.java

```java
@Configuration
public class RabbitMQConfig {
    //声明注册topic模式的交换机
    @Bean
    public TopicExchange topicExchange(){
        return new TopicExchange("topic_order_exchange",true,false);
    }

    //声明队列
    @Bean
    public Queue smsQueue(){
        return new Queue("sms.topic.queue", true);
    }
    @Bean
    public Queue duanxinQueue(){
        return new Queue("duanxin.topic.queue", true);
    }
    @Bean
    public Queue emailQueue(){
        return new Queue("email.topic.queue", true);
    }

    //完成队列与交换机之间绑定关系
    @Bean
    public Binding smsBinding(){
        return BindingBuilder.bind(smsQueue()).to(topicExchange()).with("sms.#");
    }
    @Bean
    public Binding duanxinBinding(){
        return BindingBuilder.bind(duanxinQueue()).to(topicExchange()).with("duanxin.#");
    }
    @Bean
    public Binding emailBinding(){
        return BindingBuilder.bind(emailQueue()).to(topicExchange()).with("email.#");
    }
}
```

### TopicSmsConsumer.java

```java
@Component
public class TopicSmsConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"sms.topic.queue"})
    public void receiveMessage(String message){
        System.out.println("sms接受到了订单信息是："+message);
    }
}
```

### TopicEmailConsumer.java

```java
@Component
public class TopicEmailConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"email.topic.queue"})
    public void receiveMessage(String message){
        System.out.println("email接受到了订单信息是："+message);
    }
}
```

### TopicDuanxinConsumer.java

```java
@Component
public class TopicDuanxinConsumer {
    @RabbitHandler
    @RabbitListener(queues = {"duanxin.topic.queue"})
    public void receiveMessage(String message){
        System.out.println("duanxin接受到了订单信息是："+message);
    }
}
```

## 测试

> 与上面两个案例一样，启动生产者消费者，请求  localhost:8080/topic

消费者结果

![image-20210414140257497](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414140257497.png)



# RabbitMQ高级

# RabbitMQ高级 - 过期时间TTL

## 概述

过期时间TTL表示可以对消息设置预期的时间，在这个时间内都可以被消费者接收获取；过期了之后消息会被自动删除。RabbitMQ可以对`消息和队列`设置TTL，目前有两种方法可以设置。

- 第一种方法是通过队列属性设置，队列中所有的消息都有相同的过期时间。
- 第二种方法是对消息进行单独设置，每条消息TTL可以不同。

如果上述两种方法同时使用，则消息的过期时间以两者TTL较小的那个数值为准。消息在队列的生存时间一旦超过设置的TTL值，就称为`dead message`被投递到死信队列，消费者将无法再收到该消息。

## 设置队列TTL

> 设置队列TTL

QueueTTLRabbitMQConfig.java

```java
@Configuration
public class QueueTTLRabbitMQConfig {
    // 1、声明注册direct模式的交换机
    @Bean
    public DirectExchange ttlDirectExchange() {
        return new DirectExchange("ttl_direct_exchange", true, false);
    }

    //2、队列的过期时间
    @Bean
    public Queue directTTLQueue() {
        //设置过期时间
        HashMap<String, Object> args = new HashMap<>();
        args.put("x-message-ttl", 5000);  //这里一定是int类型
        return new Queue("ttl.direct.queue", true, false, false, args);
    }

    @Bean
    public Binding ttlBinding(){
        return BindingBuilder.bind(directTTLQueue()).to(ttlDirectExchange()).with("ttl");
    }
}
```

> 启动消费者，发布消息

![image-20210414144432335](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414144432335.png)

> 发布之后5s内获取消息

![image-20210414144206883](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414144206883.png)

> 5s之后，消息消失

![image-20210414144215894](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414144215894.png)

## 设置消息TTL

> 设置消息TTL

### MessageTTLRabbitMQConfig.java

```java
@Configuration
public class MessageTTLRabbitMQConfig {
    // 1、声明注册direct模式的交换机
    @Bean
    public DirectExchange ttlMessageDirectExchange() {
        return new DirectExchange("ttl_order_exchange", true, false);
    }

    //2、队列的过期时间
    @Bean
    public Queue directTTLMessageQueue() {
        return new Queue("ttlMessage.direct.queue", true, false, false);
    }

    @Bean
    public Binding ttlMessageBinding(){
        return BindingBuilder.bind(directTTLMessageQueue()).to(ttlMessageDirectExchange()).with("ttlMessage");
    }
}
```

### MessageTTLOrderService.java

```java
@Service
public class MessageTTLOrderService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void makeOrder(String userId, String productId, int num) {
        String orderId = UUID.randomUUID().toString();
        System.out.println("订单生成成功：" + orderId);

        //通过MQ来完成消息的分发

        //给消息设置过期时间
        MessagePostProcessor messagePostProcessor = new MessagePostProcessor() {
            @Override
            public Message postProcessMessage(Message message) throws AmqpException {
                message.getMessageProperties().setExpiration("5000");//这里是字符串
                message.getMessageProperties().setContentEncoding("UTF-8");
                return message;
            }
        };
        rabbitTemplate.convertAndSend("ttl_order_exchange", "ttlMessage", orderId,messagePostProcessor);
    }
}
```

### MessageTTLOrderController.java

```java
@RestController
public class MessageTTLOrderController {
    @Autowired
    private MessageTTLOrderService orderService;

    @GetMapping("testMessageTTL")
    public void testMessageTTL(){
        orderService.makeOrder("1", "2", 1);
    }
}
```

> 启动消费者和生产者

![image-20210414145948028](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414145948028.png)

> 请求 localhost:8080/testMessageTTL,5s内查看消息。

![image-20210414145655195](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414145655195.png)

> 5s之后查看消息，消息过期已经消失。

![image-20210414145750489](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210414145750489.png)

# RabbitMQ高级 - 死信队列

## 概述

DLX，全程`Dead-Letter-Exchange`，可以称之为死信交换机，也有人称之为死信邮箱。当消息在一个队列中变成死信之后，它能被重新发送到另一个交换机中，这个交换机就是DLX，绑定DLX的队列就称之为死信队列。消息变成死信，可能是由于以下原因：

1. 消息被拒绝
2. 消息过期
3. 队列达到最大长度

DLX也是一个正常的交换机，和一般的交换机没有区别，它能在任何的队列上被指定，实际上就是设置某一个队列的属性，当这个队列中存在死信时，RabbitMQ就会自动地将这个消息发布到设置的DLX上去，进而被路由到另一个队列，即死信队列。

要想使用死信队列，只需要定义队列的时候设置队列参数`x-dead-letter-exchange`指定交换机即可。

> 代码

```java
@Configuration
public class DeadRabbitMQConfig {
    //1、声明注册direct模式的交换机 --- 死信交换机   dead_direct_exchange
    @Bean
    public DirectExchange deadDirect() {
        return new DirectExchange("dead_direct_exchange", true, false);
    }

    //声明死信队列   ----   dead.direct.queue
    @Bean
    public Queue deadQueue() {
        return new Queue("dead.direct.queue", true, false, false);
    }
    //绑定死信队列   dead  为routingKey
    @Bean
    public Binding deadBinds() {
        return BindingBuilder.bind(deadQueue()).to(deadDirect()).with("dead");
    }


    //1.声明注册direct模式的交换机，测试的业务交换机
    @Bean
    public DirectExchange ttlDirectExchange() {
        return new DirectExchange("ttl_direct_exchange", true, false);
    }

    //2.  业务队列   ---- 设置参数  ，过期时间 5s   死信存入    dead_direct_exchange 绑定  dead 的死信队列当中
    @Bean
    public Queue directTTLQueue() {

        Map<String, Object> args = new HashMap<>();
        //设置过期时间
        //args.put("x-max-length",5);
        args.put("x-message-ttl", 5000);//这里一定是int类型
        args.put("x-dead-letter-exchange", "dead_direct_exchange");
        args.put("x-dead-letter-routing-key", "dead");//fanout不需要配置
        //注意这里，  队列名称注意最好不要与之前相同，我在测试过程当中出现这个问题
        return new Queue("ttl.direct.queue2", true, false, false, args);
    }

    /**
     * 业务队列与业务交换机绑定  通过 ttl
     * @return
     */
    @Bean
    public Binding ttlBinding() {
        return BindingBuilder.bind(directTTLQueue()).to(ttlDirectExchange()).with("ttl");
    }
}
```

> ttl_direct_exchange  发布消息

![image-20210419174504445](https://i.loli.net/2021/04/19/bK7FeGuyVmgOfjB.png)

> dead.direct.queue   队列查看结果

![image-20210419174651944](https://i.loli.net/2021/04/19/fGVdP1Jr8KBAH9k.png)

> 流程图

![image-20210419180854277](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210419180854277.png)

# Demo2代码地址

https://gitee.com/liu_shaoxiong/rabbit-mq-spring-boot-demo2

# 内存磁盘的监控

## RabbitMQ内存警告

​	![image-20210420112951988](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420112951988.png)

## RabbitMQ的内存控制

当出现警告的时候，可以通过配置去修改和调整

> 命令的方式

```bash
rabbitmqctl set_vm_memory_high_watermark <fraction>
rabbitmqctl set_vm_memory_high_watermark absolute 50MB`
```

![image-20210420113405042](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420113405042.png)

> 配置文件方式  rabbitmq.conf

![image-20210420113424538](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420113424538.png)



## RabbitMQ内存换页

在某个Broker节点触及内存并阻塞生产者之前，它会尝试将队列中的消息换页到磁盘以释放内存空间。持久化和非持久化的消息都会被转储到磁盘中，其中持久化的消息本身就在磁盘中有一份副本，这里会将持久化的消息从内存中清除掉。

![image-20210420114042781](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420114042781.png)

![image-20210420114112175](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420114112175.png)

## RabbitMQ磁盘告警

`当磁盘剩余空间低于确定的阈值`时，RabbitMQ同样会阻塞生产者，这样可以避免因非持久化的消息持续换页而耗尽磁盘空间而导致服务崩溃。

![image-20210420134323417](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420134323417.png)

## RabbitMQ磁盘限制

![image-20210420134404971](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420134404971.png)

# RabbitMQ高级 - 集群

## 集群

RabbitMQ这款消息队列中间件产品本身是基于Erlang编写的，Erlang语言天生具备分布式特性（通过同步Erlang集群各节点的magic  cookie来实现）。因此，RabbitMQ天然支持Clustering。这使得RabbitMQ本身不需要像ActiveMQ、Kafka那样通过Zookeeper分别来实现HA方案和保存集群的元数据。集群是保证可靠性的一种方式，同时可以通过水平拓展以达到增加消息吞吐量能力的目的。

在实际使用过程中多采取多机多实例部署方式。为了便于练习搭建，有时候不得不在一台机器上去搭建一个rabbitmq集群，以下针对单机多实例方式来实现。

## 集群搭建

配置的前提是你的rabbitmq可以运行起来，比如`ps a|grep rabbitmq` 你能看到相关进程，又比如运行

`rabbitmqctl status`可以看到类似如下信息不报错：

![image-20210420153849382](https://gitee.com/liu_shaoxiong/pictures/raw/master/img/image-20210420153849382.png)

## 单机多实例搭建

**场景：**假设有两个rabbitmq节点，分别为rabbit-1、rabbit-2,rabbit-1作为主节点，rabbit-2作为从节点。

**启动命令：**`RABBITMQ_NODE_PORT=5672 RABBITMQ_NODENAME=rabbit-1 rabbitmq-server -detached`

**结束命令：**`rabbitmqctl -n rabbit-1 stop`

> 第一步：启动第一个节点rabbit-1

```bash
RABBITMQ_NODE_PORT=5672 RABBITMQ_NODENAME=rabbit-1 rabbitmq-server start &
```

> 第二步：启动第二个节点

```bash
RABBITMQ_NODE_PORT=5673 RABBITMQ_SERVER_START_AGES="-rabbitmq_management listener[{port,15673}]" RABBITMQ_NODENAME=rabbit-2 rabbitmq-server start &
```

> 验证启动

```bash
ps aux|grep rabbitmq
```

## 网上案例

[地址](https://blog.csdn.net/weixin_34072637/article/details/92424839)

