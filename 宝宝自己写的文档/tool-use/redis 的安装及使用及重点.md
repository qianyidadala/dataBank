# redis 的安装及使用及重点

[TOC]

### 一、安装

推荐安装在linux上面，因为毕竟服务器一般使用linux

下载到linux上，

```mysql
wget http://download.redis.io/releases/redis-4.0.6.tar.gz

tar -zxf redis-4.0.6.tar.gz
cd redis-4.0.6
make
make install
#如果报错的话下载依赖
yum install wget make gcc tcl
```

### 二、配置redis

```mysql
cd /usr/local/redis-4.0.6/bin 
./redis-server
#再打开一个客户端，因为当你结束这个客户端或者ctrl+c强行结束这个程序之后便程序关闭了
./redis-cli 
127.0.0.1:6379>ping
PONG
#意思是成功连接


#配置后台运行
cp /usr/local/src/redis-4.0.6/redis.conf /etc/redis.conf
vim /etc/redis.conf
#修改配置信息，将no改为yes
daemonize yes
#修改绑定ip将bind 127.0.0.1 改为自己ip
bind 192.168.41.22

#关闭
./redic-cli shutdown
#重新启动
./redic-servser /etc/redis.conf
#如果更改了bind的ip的话可以在后面加参数
./redic-cli # -h 192.168.21.13
```

```mysql

# 查看系统版本
cat /etc/redhat-release

# 配置防火墙
vim /etc/sysconfig/iptables

# 查看防火墙
service iptables status

# 停止防火墙
service iptables stop

# 启用防火墙
service iptables start

```

### 三、java连接Redis

```xml
//导入依赖
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>2.9.0</version>
</dependency>
```

创建项目并测试

```java
public static void main(String[] args) {
    Jedis jedis = null;
    try {
        jedis = new Jedis("192.168.21.13", 6379);
        // 连接测试
        System.out.println(jedis.ping());
    } finally {
        if (jedis != null) {
            jedis.close();
        }
    }
}
```

### 四、Redis数据类型

##### 1.String

String类型是二进制安全的。意思是redis的String可以包含任何数据。比如jpg图片或者序列化对象。String类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M。strings数据结构是简单的key-value类型，value其实不仅是string，也可以是数字。

是最常用的一种数据类型。可以享受Redis的定时持久化，操作日志及replication等功能。

常用命令

```mysql
set key value # 赋值

get key # 取值

del key # 删除

append key # 追加

strlen key # 返回长度

exists key # 判断key是否存在

move key db # 将key移动到其他数据库中

expire key 秒 # 为指定的key设置过期时间

ttl key # 查看还有多少秒过期，-1表示永不过期，-2表示已过期

type key # 查看你的key是什么类型

# 数字值命令：

incr key # 将key中储存的数字值增一
decr key # 将key中储存的数字值减一
incrby key # 将key所储存的值加上给定的增量值（increment） 
decrby key # 将key所储存的值减去给定的减量值（decrement） 

# 其他命令
getrange key startindex endindex getra # 获取指定区间值(0到-1获取全部)
setrange key startindex endindex # 设置指定区间值
setex key time(秒) # value 将值value关联到key，并将key的过期时间设为seconds(以秒为单位)
setnx key value # 只有在key不存在时设置 key 的值，如果已经存在则不设置值
mset key value [key2 value2 ...] # 同时设置一个或多个键值对
mget key [key2 key3 ...] # 获取所有(一个或多个)给定key的值
msetnx key value [key2 value2 ...]  # 同时设置一个或多个键值对（key不存在时）在设置的过程中只要有一个失败，其他的都不会set成功
getset key value # 获取旧值，设置新值
```

##### 2.List

Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。它的底层实际是个链表。

**应用场景**：

twitter的关注列表，粉丝列表等都可以用Redis的list结构来实现。Lists 就是链表，相信略有数据结构知识的人都应该能理解其结构。使用Lists结构，我们可以轻松地实现最新消息排行等功能。Lists的另一个应用就是消息队列，

可以利用Lists的PUSH操作，将任务存在Lists中，然后工作线程再用POP操作将任务取出进行执行。Redis还提供了操作Lists中某一段的api，你可以直接查询，删除Lists中某一段的元素。

**基本命令**：

```mysql
lpush key value [value2 ...] # 将一个或多个值插入到列表头部(允许值重复)
```

```mysql
rpush key value [value2 ...] # 将一个或多个值插入到列表尾部(允许值重复)
```

```mysql
lrange key startindex endindex # 获取列表指定范围内的元素(0到-1获取全部)
```

```mysql
lindex key index # 通过索引获取列表中的元素
```

```mysql
llen key # 获取指定key值的个数
```

```mysql
lpop key # 移出并获取列表的第一个元素
```

```mysql
rpop key # 移除并获取列表最后一个元素
```

**其他命令**：

```mysql
lrem key count value # 删除count个value
```

```mysql
ltrim key start end # 截取指定范围的值后再赋值给key（让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除）

lset key index value # 通过索引设置列表元素的值

linsert key BEFORE|AFTER pivot value # 在列表指定的元素前或者后插入元素
```

##### 3.Hash

Redis hash 是一个键值对集合。hash是一个string类型的field和value的映射表，**hash特别适合用于存储对象**。类似Java里面的Map。

应用场景：
在Memcached中，我们经常将一些结构化的信息打包成HashMap，在客户端序列化后存储为一个字符串的值，比如用户的昵称、年龄、性别、积分等，这时候在需要修改其中某一项时，通常需要将所有值取出反序列化后，修改某一项的值，再序列化存储回去。这样不仅增大了开销，也不适用于一些可能并发操作的场合（比如两个并发的操作都需要修改积分）。而Redis的Hash结构可以使你像在数据库中Update一个属性一样只修改某一项属性值。

基本命令：

````mysql
hset key field value # 以hash方式存储

hget key field # 以hash方式获取

hmset key field value # [field value ...] 以hash方式存储一个或者多个

hmget key field # [field ...] 以hash方式获取一个或者多个

hgetall key # 获取在哈希表中指定key的所有字段和值

hdel key field [field ...] # 删除一个或多个哈希表字段

hkeys key # 获取所有哈希表中的字段

hvals key # 获取哈希表中所有值

````

**其他命令：**

```mysql
hlen key # 获取哈希表中字段的数量

hexists key # 查看哈希表key中，指定的字段是否存在

hincrby key field value # 为哈希表key中的指定字段的整数值加上增量value(必须为数字)

hincrbyfloat key field value # 为哈希表key中的指定字段的浮点数值加上增量value 

```

##### 4.set

 Set是string类型的无序集合。**它是通过HashTable实现实现的**。

应用场景：
Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。Sets 集合的概念就是一堆不重复值的组合。利用Redis提供的Sets数据结构，可以存储一些集合性的数据，比如在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis还为集合提供了求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能，对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。

基本命令：

```mysql
sadd key value [value2 ...] # 向集合添加一个或多个成员

smembers key # 返回集合中的所有成员

sismember key member # 判断 member 元素是否是集合 key 的成员

scard key # 获取集合里面的元素个数

srem key value # 删除集合中指定元素

spop key [count] # 移除集合中一个随机元素并返这个元素（可选择删除数量）

srandmember key [coutn] # 随机返回集合中一个或多个值

smove key1 key2 # 将key1里的某个值赋给key2
```

**其他命令：**

```mysql
sdiff key1 [key2] # 返回给定所有集合的差集

sinter key1 [key2] # 返回给定所有集合的交集

sunion key1 [key2] #返回所有给定集合的并集
```

##### 5.zset

zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

应用场景：
Redis sorted set的使用场景与set类似，区别是set不是自动有序的，而sorted set可以通过用户额外提供一个优先级(score)的参数来为成员排序，并且是插入有序的，即自动排序。当你需要一个有序的并且不重复的集合列表，那么可以选择sorted set数据结构，比如twitter 的public timeline可以以发表时间作为score来存储，这样获取时就是自动按时间排好序的。还可以用Sorted Sets来做带权重的队列，比如普通消息的score为1，重要消息的score为2，然后工作线程可以选择按score的倒序来获取工作任务。让重要的任务优先执行。

基本命令：

```mysql
zadd key score1 member1 [score2 member2] #向有序集合添加一个或多个成员，或者更新已存在成员的分数

zrange key startindex endindex [WITHSCORES] #通过索引区间返回有序集合成指定区间内的成员(WITHSCORES带出分数)

zscore key member #返回有序集中，成员的分数值

zrangebyscore key min max WITHSCORES #通过分数返回有序集合指定区间内的成员

zrem key member [member ...] #删除一个或多个元素

zcard key #获取有序集合的成员数
```

**其他命令：**

```mysql
zcount key min max #计算在有序集合中指定区间分数的成员数

zrank key member #返回有序集合中指定成员的索引

zrevrank key member #返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序

zrevrange key startindex endindex [WITHSCORES] #返回有序集中指定区间内的成员，通过索引，分数从高到底

zrevrangebyscore key max min [WITHSCORES] #返回有序集中指定分数区间内的成员，分数从高到低排序
```

### 五、Redis的常用配置。

##### 1.includes（包含）

```mysql
include /path/to/cther.conf #这在你有标准配置模板但是每个redis服务器又需要个性设置的时候很有用
```

##### 2.general（一般常用）

```mysql
daemoize yes #redis默认不是以守护进程启动的，所以可以通过这个配置打开
```

```mysql
protected-mode yes #保护模式，默认开启，开启该参数后redis只会本地进行访问，拒绝外部访问。
```

~~~
pidfile /var/run/redis/redis-server.pid redis的进程文件
~~~

```mysql
port 6379 #redis监听的端口号
```

```mysql
tcp-backlog 511 #TCP连接队列的长度
```

```mysql
bind 127.0.0.1 #指定redis只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求
```

```mysql
timeout 0 #当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
```

```mysql
loglevel notice #指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose;
#debug（很多信息，方便开发、测试）
#verbose（许多有用的信息，但是没有debug级别信息多）
#notice（适当的日志级别，适合生产环境）
#warn（只有非常重要的信息）

```

```mysql
logfile /var/log/redis/redis-server.log #日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null。空字符串的话，日志会打印到标准输出设备。后台运行的redis标准输出是/dev/null

databases 16 #设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
```

##### 3.redis.conf常见配置

redis.conf 配置项说明如下：

```mysql
# Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
daemonize no

# 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
pidfile /var/run/redis.pid

# 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
port 6379

# 绑定的主机地址
bind 127.0.0.1

# 当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
timeout 300

# 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
loglevel verbose

# 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
logfile stdout

# 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
databases 16

# 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
save <seconds> <changes>

# Redis默认配置文件中提供了三个条件：
    #save 900 1 # 900秒（15分钟）内有1个更改
    #save 300 10 # 300秒（5分钟）内有10个更改
    #save 60 10000 # 60秒内有10000个更改
# 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
rdbcompression yes

# 指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

# 指定本地数据库存放目录
dir ./

# 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
slaveof <masterip> <masterport>

# 当master服务设置了密码保护时，slav服务连接master的密码
masterauth <master-password>

# 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
requirepass foobared

# 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
maxclients 128

# 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
maxmemory <bytes>

# 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
appendonly no

# 指定更新日志文件名，默认为appendonly.aof
appendfilename appendonly.aof

# 指定更新日志条件，共有3个可选值： 
    #no：表示等操作系统进行数据缓存同步到磁盘（快） 
    #always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    #everysec：表示每秒同步一次（折衷，默认值）
    #appendfsync everysec

# 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
vm-enabled no

# 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
vm-swap-file /tmp/redis.swap

# 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
vm-max-memory 0

# Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
vm-page-size 32

# 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
vm-pages 134217728

# 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
vm-max-threads 4

# 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
glueoutputbuf yes

# 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    #hash-max-zipmap-entries 64
    #hash-max-zipmap-value 512
# 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
activerehashing yes

# 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
include /path/to/local.conf

```

配置信息可以通过`config get` 获取

```mysql
config get port # 获取redis端口号
config get dir # 获取启动路径
config get requirepass # 获取密码
config set requirepass "123456" # 设置redis密码

```

### 六、持久化（RDB/AOF）

#### 1.RDB(Redis Database) 是Redis默认的持久化方案。

它可以在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复是是将快照文件直接读到内存里，在RDB方式下，你有两种选择：

- 手动化持久数据命令来让redis进行一次数据快照。
- 根据配置的sh脚本文件（shell script）来达到自动持久化数据。

**持久化数据的方法（save，bgsave）**：

- **save**命令

   **save命令执行时只管保存，不能进行其他操作** 。save操作在redis主线程中工作，因此会阻塞其他请求操作，应该避免使用。默认情况下保存到dump.rdb文件，当redis重启后自动读取dump文件。（通常情况下一千万的字符串类型键，1GB的快照文件，同步到内存中的时间是20~30秒）

- **bgSave**命令

  **bgSave后台备份，在备份的同时可以处理输入的数据**，bgSave操作则是条用Fork，产生子进程，父进程继续处理请求。子进程将数据写入临时文件，并在写完后，替换原有的.rbd文件。Fork发生时，父子进程内存共享，所有为了不影响子进程做数据快照，在这期间修改的数据，将会被复制一份，而不进共享内存。所以说，RDB所持久化的数据，是Fork发生时的数据。如果你的实际情况对数据丢失没那么敏感，丢失的也可以从传统数据库中获取或者说丢失部分也无所谓，那么你可以选择RDB持久化方式。

```mysql
## 系统默认
# 15分钟修改一次
save 900 1 
# 五分钟修改10次
save 300 10
# 一分钟修改一万次
save 60 10000

## 如果想要禁用
# save ""
```

2.