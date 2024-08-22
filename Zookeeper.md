# Zookeeper

### Zookeeper的概念

Zookeeper是一个树形目录服务

三大功能：配置管理，分布式锁，集群管理

### Zookeeper的安装

mac：直接用homebrew

window：下载包然后解压找bin文件

### Zookeeper的命令操作

1. Zookeeper数据模型

   树形目录结构，每个节点都有数据和节点信息

   四大类型节点：持久化，临时 -e，持久顺序-s，临时顺序 -es

2. Zookeeper服务端常用命令

   启动：zkServer start

   查看：zkServer status

   停止：zkServer stop

   重启：zkServer restart

   查看版本：zkServer version

3. Zookeeper客户端命令

   连接服务端：zkCli -server localhost:2181

   查看路径内容：ls -s(详细) /

   创建节点：create /app1 huangkaiwen

   获取数据：get /app1

   设置数据：set /app1 黄凯文

   删除节点：delete /app1

   查看帮助：help

#### JavaAPI操作

### Zookeeper分布式锁的原理

核心思想：当客户端想要获取锁，则创建节点，使用完锁，则删除节点

1. 客户端获取锁时，在lock节点下创建顺序节点

2. 然后获取lock下面的所有子节点，客户端获取到所有子节点之后，如果发现自己的子节点序号最小，则该客户端获取到了锁，使用完锁将节点删除

3. 如果发现自己的节点不是最小的，则找到比自己小的节点监测删除事件
4. 如果小节点被删除，则watch收到通知，再次判断自己节点是不是最小的，如果是则获取到锁

### Zookeeper集群搭建

**1.节点启动**：每个Zookeeper节点启动后都会进入LOOKING状态，表示正在寻找Leader。

**2.投票过程**：每个节点都会发起一次投票。初始时每个节点都投自己，投票信息包括（myid, zxid）。zxid是事务ID，表示事务的顺序号。

**3.接收投票**：每个节点接收其他节点的投票。如果接收到的投票的zxid更大，或zxid相同但myid更大，则更新自己的投票。

**4.统计票数**：每个节点统计投票结果。如果某个投票获得超过半数的支持，该节点就被选为Leader。

**5.Leader确认**：一旦某个节点成为Leader，其他节点会更新自己的状态为FOLLOWING（Follower）。此时集群进入正常工作状态，Leader负责处理客户端的写请求，Follower负责处理读请求并将写请求转发给Leader。
