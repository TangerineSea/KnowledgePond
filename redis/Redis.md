# Redis

## 一、常用五大数据类型

### 1.1 Redis键(key)

| 命令          | 含义                                                         |
| :------------ | :----------------------------------------------------------- |
| keys *        | 查看当前库所有key （匹配： keys *1）                         |
| exists key    | 判断某个key是否存在                                          |
| type key      | 查看你的key是什么类型                                        |
| del key       | 删除指定的key数据                                            |
| unlink key    | 根据value选择非阻塞删除(仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作。) |
| expire key 10 | 10秒钟：为给定的key设置过期时间                              |
| ttl key       | 查看还有多少秒过期，-1表示永不过期，-2表示已过期             |
| select        | 命令切换数据库                                               |
| dbsize        | 查看当前数据库的key的数量                                    |
| flushdb       | 清空当前库                                                   |
| flushall      | 通杀全部库                                                   |

### 1.2 Redis字符串(String)

#### 1.2.1 简介

1. String是Redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。

2. String类型==是二进制安全的==。意味着Redis的string可以包含任何数据。比如jpg图片或者序列化的对象。

3. String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是==512M==

#### 1.2.2 常用命令	

*NX:	当数据库中key不存在时，可以将key-vlaue添加数据库

*XX:	当数据库中key存在时，可以将key-vlaue添加数据库，与NX参数互斥

*EX: 	key的超时秒数

*PX:	key的超时毫秒数，与EX互斥

| 命令                                    | 含义                                                         |
| :-------------------------------------- | ------------------------------------------------------------ |
| set <key><value>                        | 添加键值对                                                   |
| get <key>                               | 查询对应键值                                                 |
| append <key><value>                     | 将给定的<value>追加到原值的末尾                              |
| strlen <key>                            | 获得值得长度                                                 |
| incr <key>                              | 将key中存储的数字值增1，只能对数字值操作，如果为空，新增值为1 |
| decr <key>                              | 将key中储存的数字值减1，只能对数字值操作，如果为空，新增值为-1 |
| incrby / decrby <key><步长>             | 将key中储存的数字值增减。自定义步长                          |
| mset <key1><value1><key2><value2> ...   | 同时设置一个或多个key-value对                                |
| mget <key1><key2><key3> ...             | 同时获取一个或多个value                                      |
| msetnx <key1><value1><key2><value2> ... | 同时设置一个或多个key-value对，当且仅当所有给定key都不存在。==原子性，有一个失败则都失败== |
| getrange <key><起始位置><结束位置>      | 获得值得范围，类似java中的substring，前包，后包              |
| setrange <key><起始位置><value>         | 用<value>覆写<key>所储存的字符串值，从<起始位置>开始(==索引从0开始==)。 |
| setex <key> <过期时间><value>           | 设置键值的同时，设置过期的时间，单位秒。                     |
| getset <key><value>                     | 以新换旧，设置了新值同时获得旧值。                           |



>==原子性==
>
>INCR key
>
>对存储在指定key的数值执行原子的加1操作
>
>所谓原子操作是指不会被线程调度机制打断的操作；
>
>这种操作一旦开始，就一直运行到结束，中间不会有任何context switch（切换到另一个线程）。
>
>(1) 在单线程中，能够在单条指令中完成的操作都是可以认为是“原子操作”，因为中断只能发生于指令之间。
>
>(2) 在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。
>
>Redis单命令的原子性主要得益于Redis的单线程。



#### 1.2.3 数据结构

String的数据结构为简单动态字符串(Simple Dynamic String,缩写SDS)。是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配。

![image-20220908223602545](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220908223602545.png)

如图中所示，内部为当前字符串实际分配的空间capacity一般要高于实际字符串长度len。当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次智慧多扩1M的空间。需要注意的是字符串最大长度为512M。

### 1.3 Redis列表(List)

#### 1.3.1 简介

单键多值

Redis列表是简单的字符串列表，按照插入顺序排序，你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

它的底层实际是个==双向链表==，对两的操作性能很高，通过索引下标的操作中间的节点性能会较差。

![image-20220911113159180](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911113159180.png)

#### 1.3.2 常用命令

| 命令                                           | 含义                                             |
| ---------------------------------------------- | ------------------------------------------------ |
| lpush/rpush <key><value1><value2><value3>  ... | 从左边/右边插入一个或多个值                      |
| lpop/rpop <key>                                | 从左边/右边吐出一个值。 ==值在键在，值光键亡==   |
| rpoplpush <key1><key2>                         | 从<key1>列表右边吐出一个值，插到<key2>列表左边   |
| lrange <key><start><stop>                      | 按照索引下标获得元素(从左到右，0 -1表示获取所有) |
| lindex <key><index>                            | 按照索引下标获得元素(从左到右)                   |
| llen <key>                                     | 获得列表长度                                     |
| linsert <key> after/before <value><newvalue>   | 在<value>的后面/前面插入<newvalue>插入值         |
| lrem <key><n><value>                           | 从左边删除n个value(从左到右)                     |
| lset <key><index><value>                       | 将列表key下标为index的值替换成value              |



#### 1.3.3 数据结构

List的数据结构为快速链表quickList。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表。

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成quicklist。

因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev何next。

![image-20220911121724927](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911121724927.png)

​	Redis将链表何ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。



### 1.4 Redis集合（Set）

#### 1.4.1 简介

Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以==自动排重==的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择,并且set提供了判断某个成员是否子啊一个set集合内的重要接口，这个也是list所不能提供的。

Redis的Set时string类型的==无序集合。他底层其实是一个value为null的hash表==，所以添加，删除，查找的==复杂度都是O(1)==。

一个算法，随着数据的增加，执行时间的长短，如果是O(1),数据增加，查找数据的时间不变

#### 1.4.2 常用命令

| 命令                               | 含义                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| sadd <key><value1><value2> ...     | 将一个或多个member元素加入到集合key中，已经存在的member元素将被忽略。 |
| smembers <key>                     | 取出该集合的所有值。                                         |
| sismember <key><value>             | 判断集合<key>是否为含有该<value>的值，有1，没有0             |
| scard <key>                        | 返回该集合的元素个数。                                       |
| srem <key><value1><value2> ...     | 删除集合中的某个元素。                                       |
| spop <key>                         | 随机从该集合中吐出一个值。                                   |
| srandmember <key><n>               | 随机从该集合中取出n个值。不会从集合中删除。                  |
| smove <source><destination><value> | 把集合中一个值从一个集合移动到另一个集合                     |
| sinter <key1><key2>                | 返回两个集合的==交集==元素。                                 |
| sunion <key1><key2>                | 返回两个集合的==并集==元素。                                 |
| sdiff <key1><key2>                 | 返回两个集合的==差集==元素(key1中的，不包含key2中的)         |

#### 1.4.3 数据结构

Set数据结构是dict字典，字典是用哈希表实现的。

Java中HashSet的内部实现使用的是HashMap，只不过所有的value都指向同一个对象。Redis的set结构也是一样，它的内部也是用hash结构，所有value都指向同一个内部值。

### 1.5 Redis哈希(Hash)

#### 1.5.1 简介

Redis hash是一个键值对集合

Redis hash是一个string类型的==field==和==value==的映射表，hash特别适合用于存储对象。

类似Java里面的Map<String,Object>

用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储。

主要有以下2中存储方式：

![image-20220911140431216](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911140431216.png)

通过==kye(用户ID)+field(属性标签)==就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题。

#### 1.5.2 常用命令

| 命令                                            | 含义                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| hset <key><field><value>                        | 给<key>集合中的<field>键赋值<value>                          |
| hget <key1><field>                              | 从<key1>集合<field>取出value                                 |
| hmset <key1><field1><value1><field2><value2>... | 批量设置hash的值                                             |
| hexists <key1><field>                           | 查看哈希表key中，给定域field是否存在。                       |
| hkeys <key>                                     | 列出该hash集合的所有field                                    |
| hvals <key>                                     | 列出该hash集合的所有value                                    |
| hincrby <key><field><increment>                 | 为哈希表key中的域field的值加上增量1 -1                       |
| hsetnx <key><field><value>                      | 将哈希表key中的域field的值设置为vlaue，当且仅当域field不存在。 |

 #### 1.5.3 数据结构

Hash类型对应的数据结构是两种：ziplist(压缩列表)，hashtable(哈希表)。当field-value长度较短且个数较少时，使用ziplist，否则使用hsahtable。

### 1.6 Redis有序集合Zset(sorted set)

#### 1.6.1 简介

Redis有序集合zset与普通集合set非常相似，是一个==没有重复元素==的字符串集合。

不同之处是有序集合的没个成员都关联了一个==评分(score)==，这个评分(score)被用来按照从最低分到最高分的方式排序集合中的成员。==集合的成员是唯一的，但是评分可以是重复了==。

因为元素是有序的，所以你也可以很快的根据评分(score)或者次序(position)来获取一个范围的元素。

访问有序集合的中间元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表。

#### 1.6.2 常用命令

| 命令                                                         | 含义                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| zadd <key><score1><value1><score2><value2>...                | 将一个或多个member元素及其score值加入到有序集key当中。       |
| zrange <key><start><stop> [WITHSCORES]                       | 返回有序集key中，下标在<start><stop>之间的元素带WITHSCORES,可以让分数一起和值返回到结果集。 |
| zrangebyscore key minmax [withscores] [limit offset count]   | 返回有序集key中，所有score值介于min和max之间(包括等于min或max)的成员。有序集成员按score值递增（从小到大）次序排列。 |
| zrevrangebyscore key maxmin [withscores] [limit offset count] | 返回有序集key中，所有score值介于min和max之间(包括等于min或max)的成员。有序集成员按score值递减（从大到小）次序排列。 |
| zincrby <key><increment><value>                              | 为元素的score加上增量                                        |
| zrem <key><value>                                            | 删除该集合下，指定值的元素                                   |
| zcount <key><min><max>                                       | 统计该合集，分数区间的元素个数                               |
| zrank <key><value>                                           | 返回该值在集合中的排名，从0开始。                            |

#### 1.6.3 数据结构

SortedSet(zset)是Redis提供的一个非常特别的数据结构，一方面它等价于Java的数据结构Map<String, Double>，可以给每一个元素value赋予一个权重score，另一方面它又类似于TreeSet,内部的元素会按照权重score进行排序，可以得到没个元素的名次，还可以通过score的范围来获取元素的列表。

zset底层使用了两个数据结构

（1）hash，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值。

（2）跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表。

#### 1.6.4 跳跃表（跳表）

1. 简介

​		有序集合在生活中比较常见，例如根据成绩对学生，根据得分对玩家排名等。对于有序集合的底层实现，可以用数组、平衡树、链表等。数组不便元素的插入、删除；平衡树或红黑树虽然效率高但结构复杂；链表查询需要遍历所有效率低。Redis采用的是跳跃表。跳跃表效率堪比红黑树，实现远比红黑树简单。

2. 实例

   对比有序链表和跳跃表，从链表中查询出51

   （1）有序链表

   ![image-20220911150959711](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911150959711.png)

​			要查找值为51的元素，需要从第一个元素开始一次查找、比较才能找到。共需要6次比较。

​		（2）跳跃表

![image-20220911151150496](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911151150496.png)

从第2层开始，1节点比51节点小，向后比较。

21节点比51节点小，继续向后比较，后面就是NULL了，所以从21节点向下到第1层。

在第1层，41节点比51节点小，继续向后，61节点比51节点大，所以从41向下。

在第0层，51节点为要查找的节点，节点被找到，共查找4次。



从此可以看出跳跃表比有序链表效率要高

## 二、Redis配置文件

### 

## 三、Redis的发布和订阅

### 3.1 什么是发布和订阅

Redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息。

Redis客户端可以订阅任意数量的频道。

### 3.2 Redis的发布和订阅

1. 客户端可以订阅频道如下图

![image-20220911191828463](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911191828463.png)



2. 当给这个频道发布消息后，消息就会发送给订阅的客户端

![image-20220911191910634](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911191910634.png)



### 3.3 发布订阅命令行实现

1. 打开一个客户端订阅channel1

   SUBSCRIBE   channel1

   ![image-20220911192351832](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911192351832.png)

2. 打开另一个客户端，给channel1发布消息hello

​		publish channel1 hello

![image-20220911192629680](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911192629680.png)

​		返回的1是订阅者的数量

3. 打开第一个客户端可以看到发送的消息

![image-20220911192658699](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20220911192658699.png)



## 四、Redis新数据类型

### 4.1、Bitmaps

合理地使用操作位能够有效的提高内存使用率和开发效率。

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：

（1）Bitmaps本事不是一种数据类型，实际上它就是字符串(key-value)，但是它可以对字符串的位进行操作。

（2）Bitmaps单独提供了一套命令，所以在Redis中使用Bitmaps和使用字符串的方法不太相同。可以把Bitmaps想象成一个以位为单位的数组，数组的每个单元只能存储0和1，数组的下标在Bitmaps中叫做偏移量。

#### 4.1.1 命令

1. setbit

​		setbit <key> <offset> <value>设置Bitmaps中某个偏移量的值（0或1）

​		*offset：偏移量从0开始

2. getbit

​		getbit <key> <offset> 获取Bitmaps中某个偏移量的值

​		获取键的第offset位的值（从0开始算）

3. bitcount

​		统计**字符串**被设置成1的bit数。一般情况下，给定的整个字符串都会被进行计数，通过指定额外的start或end参数，可以让计数只在特定的位上进行。start和end参数的设置，都可以使用负数值：比如-1表示最后一个位，而-2表示倒数第二个位，start、end是指bit组的字节的下标数，二者皆包含。

​		（1）格式

​			bitcount <key> [start end] 统计字符串从start字节到end字节比特值为1的数量

4. bitop

​		（1）格式

​			bitop and(or/not/xor) <destkey> [key...]

​		bitop是一个符合操作，它可以做多个Bitmaps的and（交集）、or（并集）、not（非）、xor（异或）操作并将结果保存在destkey中。

​		计算出任意一天都访问过网站的用户数量（例如月活跃量就是类似这种），可以使用or求并集

#### 4.1.2 Bitmaps与set对比

![image-20221106180008743](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106180008743.png)

![image-20221106180126423](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106180126423.png)

![image-20221106180157126](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106180157126.png)



#### **总结：**

Bitmaps本身并不是一种数据类型，只是一个字符串，专门进行位操作的字符串，它里面的优势跟set相比，可以极大的节约空间，极大的提高cpu或内存的利用率。



### 4.2、HyperLogLog

#### 4.2.1 简介

​		Redis HyperLogLog 是用来做基数统计的算法，HtyperLogLog的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

​		在Redis里面，每个HyperLogLog键只需要花费12KB内存，就可以计算接近2^64个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

​		但是，因为HyperLogLog只会根据输入元素来计算基数，而不会储存输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素。

​		什么是基数？

​		比如数据集{1，3，5，7，5，7，8}，那么这个数据集的基数集为{1，3，5，7，8}，基数（不重复元素）为5。基数估计就是在误差可接受的范围内，快速计算基数。

#### 4.2.2 命令

1. pfadd

​		（1）格式

​			pfadd <key> <element> [element...] 添加指定元素到 HyperLogLog中

​		（2）实例

![image-20221106192951408](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106192951408.png)

​				将所有元素添加到指定HyperLogLog数据结构中。如果执行命令后HLL估计的近似基数发生变化，则返回1，否则返回0。

2. pfcount

​		（1）格式

​				pfcount <key> [key...] ==计算HLL的近似基数==，可以计算多个HLL，比如用HLL存储每天的UV，计算一周的UV可以使用7天的UV合并计算即可

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106193507096.png" style="zoom:80%;" />

​		（2）实例

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106194350630.png" alt="image-20221106194350630" style="zoom:80%;" />

3. pfmerge

​	（1）格式

​		pfmerge <destkey> <sourcekey> [sourcekey ...] 将一个或多个HLL合并后的结果存储在另一个HLL中，比如每月活跃用户可以使用每天的活跃用户来合并计算可得

![image-20221106195105751](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106195105751.png)

​	（2）实例

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106195133378.png" alt="image-20221106195133378" style="zoom:80%;" />

### 4.3、Geospatial

#### 4.3.1 简介

​		Redis 3.2中增加了对GEO类型的支持。GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

#### 4.3.2 命令

1. geoadd

​	（1）格式

​			geoadd <key> <longitude> <member> [longitude latitude member ...] 添加地理位置（精度，纬度，名称）

![image-20221106195903824](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106195903824.png)

​	（2）实例

​			geoadd china:city 121.47 31.23 shanghai

​			geoadd china:city 106.50 29.53 chongqing 114.05 22.52 shenzhen 116.38 39.90 beijing

![image-20221106200539978](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106200539978.png)

​		两极无法直接添加，一般会下载城市数据，直接通过Java程序一次性导入。

​		有效的经度从-180度到180度。有效的纬度从-85.05112878度到85.05112878度。

​		当坐标位置超出指定范围时，该命令将会返回一个错误。

​		已经添加的数据，是无法再次往里面添加的。

2. geopos

​		（1）格式

​			geopos <key> <member> [member ...] 获得指定地区的坐标值

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106200726724.png" alt="image-20221106200726724" style="zoom:80%;" />

​		（2）实例

![image-20221106200902601](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106200902601.png)

3. geodist

​	（1）格式

​		geodist <key> <member1> <member2> [m|km|ft|mi ] 获取两个位置之间的直线距离

![image-20221106201022237](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106201022237.png)

​	（2）实例

​		获取两个位置之间的直线距离

![image-20221106201303155](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106201303155.png)

​		单位：

​		m 表示单位为米【默认值】。

​		km 表示单位为千米。

​		mi 表示单位为英里。

​		ft 表示单位为英尺。

​		如果用户没有显示地指定单位参数，那么GEODIST默认使用米作为单位

4. georadius

​	（1）格式

​		georadius <key> < longitude> <latitude> radius 	m|km|ft|mi	以给定的经纬度为中心，找出某一半径内的元素

![image-20221106201608429](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106201608429.png)

经度	纬度	距离	单位

​	（2）实例

![image-20221106201651010](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106201651010.png)



## 五、Redis_Jedis_测试

### 5.1、Jedis所需要的jar包

```java
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>
```

### 5.2、连接Redis注意事项

禁用Linux的防火墙：Linux(CentOS7)里执行命令

**systemctl stop/disable firewalld.server**

redis.conf中注释掉==bind 127.0.0.1==，然后==protected-mode no==

### 5.3 Jedis常用操作

#### 5.3.1 创建动态的工程

#### 5.3.2 创建测试程序

```java
package com.atguigu.jedis;

import redis.clients.jedis.Jedis;

public class JedisDemo1 {
    public static void main(String[] args) {
        // 创建Jedis对象
        Jedis jedis = new Jedis("192.168.254.130",6379);
        // 测试
        String value = jedis.ping();
        System.out.println("连接成功：" + value);
        jedis.close();
    }
}

```

### 5.4 测试相关数据类型

#### 5.4.1 Jedis-API：	Key

```java
// 操作key
@Test
public void demo1() {
    // 创建Jedis对象
    Jedis jedis = new Jedis("192.168.254.130",6379);
    // 添加值
    jedis.set("name","jack");
    // 获取
    String name = jedis.get("name");
    System.out.println(name);
    Set<String> keys = jedis.keys("*");
    for (String key : keys) {
        System.out.println(key);
    }
    System.out.println("判断是否存在：" + jedis.exists("k1"));
    System.out.println("查看过期时间：" + jedis.ttl("k1"));
    System.out.println("获取里面的值：" + jedis.get("k1"));
}
```

#### 5.4.2 Jedis-API：	String

```java
jedis.mset("a1","s1","a2","s2");
System.out.println("a1","a2");
```

#### 5.4.3 Jedis-API：	List

```java
List<String> list = jedis.lrange("mylist",0,-1);
for (String element : list) {
	System.out.println(element);
}
```

#### 5.4.4 Jedis-API：	set

```java
jedis.sadd("orders","order01");
jedis.sadd("orders","order02");
jedis.sadd("orders","order03");
jedis.sadd("orders","order04");
Set<String> smembers = jedis.smembers("orders");
for (String order : smembers) {
    System.out.println(order);
}
jedis.srem("order","order02");
```

#### 5.4.4 Jedis-API：	hash

```java
jedis.hset("hash1","userName","lisi");
System.out.println(jedis.hget("hash1","userName"));
Map<String, String> map = new HashMap<String, String>();
map.put("telphone","13810169999");
map.put("address","atguigu");
map.put("email","abc@163.com");
jedis.hmset("hash2", map);
List<String> result = jedis.hmget("hash2", "telphone", "email");
for (String element : result) {
	System.out.println(element);
}
```

#### 5.4.4 Jedis-API：	zset

```java
@Test
public void demo5() {
    // 创建Jedis对象
    Jedis jedis = new Jedis("192.168.254.130",6379);
    jedis.zadd("china", 100d, "shanghai");
    Set<String> china = jedis.zrange("china", 0, -1);
    System.out.println(china);
}
```

## 六、Redis_Jedis_实例

### 6.1 完成一个手机验证码功能

要求：

1. 输入手机号，点击发送后随机生成6位数字码，2分钟有效。
2. 输入验证码，点击验证，返回成功或失败。
3. 每个手机号每天只能输入3次。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221106220942725.png" alt="image-20221106220942725" style="zoom:80%;" />



## 七、Springboot整合Redis

### 7.1 整合步骤

1. 在pom.xml中引入依赖

```xml
<!-- redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- spring2.X集成redis所需common-pool2 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.6.0</version>
</dependency>
```

2. 在application.properties中配置redis配置

```properties
# Redis服务器地址
spring.redis.host=192.168.254.130
# Redis服务器连接端口
spring.redis.port=6379
# Redis数据库索引（默认为0）
spring.redis.database=0
# 连接超时时间（毫秒）
spring.redis.timeout=1800000
# 连接池最大连接数（使用负值没有限制）
spring.redis.lettuce.pool.max-active=20
# 最大阻塞等待时间（负数表示没限制）
spring.redis.lettuce.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
# 连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

3. 添加redis配置类

```java
import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;

@EnableCaching  // 开启缓存类
@Configuration  // 配置类
public class RedisConfig extends CachingConfigurerSupport {

@Bean
public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
    RedisTemplate<String, Object> template = new RedisTemplate<>();
    RedisSerializer<String> redisSerializer = new StringRedisSerializer();
    Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
    ObjectMapper om = new ObjectMapper();
    om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
    jackson2JsonRedisSerializer.setObjectMapper(om);
    template.setConnectionFactory(factory);
    //key序列化方式
    template.setKeySerializer(redisSerializer);
    //value序列化
    template.setValueSerializer(jackson2JsonRedisSerializer);
    //value hashmap序列化
    template.setHashValueSerializer(jackson2JsonRedisSerializer);
    return template;
}

@Bean
public CacheManager cacheManager(RedisConnectionFactory factory) {
    RedisSerializer<String> redisSerializer = new StringRedisSerializer();
    Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
    //解决查询缓存转换异常的问题
    ObjectMapper om = new ObjectMapper();
    om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
    jackson2JsonRedisSerializer.setObjectMapper(om);
    // 配置序列化（解决乱码的问题）,过期时间600秒
    RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofSeconds(600))
            .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
            .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
            .disableCachingNullValues();
    RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
            .cacheDefaults(config)
            .build();
    return cacheManager;
}
}
```

4. 在controller中编写测试类

```java
@RestController
@RequestMapping("/redisTest")
public class RedisTestController {

@Autowired
private RedisTemplate redisTemplate;

@GetMapping
public String testRedis() {
    // 设置值到redis
    redisTemplate.opsForValue().set("name","lucy");
    // 从redis获取值
    String name = (String) redisTemplate.opsForValue().get("name");
    return name;
	}
}
```



## 八、Redis6的事务操作

### 8.1、Redis的事务定义

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的主要作用就是==串联多个命令==防止别的命令插队。



### 8.2、Multi、Exec、discard

从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，知道输入Exec后，Redis会将之前的命令队列中的命令一次执行。

组队的过程中可以通过discard来放弃组队。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110134153102.png" alt="image-20221110134153102" style="zoom:50%;" />

组队成功，提交成功：

![image-20221110133955213](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110133955213.png)

组队成功，放弃组队：

![image-20221110134311862](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110134311862.png)

### 8.3、事务的错误处理

1. 组队中某个命令出现了报告错误，执行时整个的所有队列都会被取消。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110134659035.png" alt="image-20221110134659035" style="zoom:50%;" />

效果：

![image-20221110134845879](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110134845879.png)

2. 如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110134604717.png" alt="image-20221110134604717" style="zoom:50%;" />

效果：

![image-20221110135048130](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110135048130.png)

### 8.4、为什么要做成事务

想想一个场景：有很多人有你的账户，同时去参加双十一抢购

### 8.5、事务冲突的问题

#### 8.5.1、例子

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110135727264.png" alt="image-20221110135727264" style="zoom:50%;" />

#### 8.5.2、悲观锁

悲观锁（Pessimistic Lock），顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。==传统的关系型数据库里边就用到了很多这种锁机制==，比如**行锁，表锁**等，**读锁，写锁**等，都是在操作之前先上锁。

==效率有点低，只能一个人操作完，在进行另一个人的操作==

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110140027337.png" alt="image-20221110140027337" style="zoom:50%;" />

#### 8.5.3、乐观锁

乐观锁（Optimistic Lock），顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。==乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种check-and-set机制实现事务的。==

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221110141010814.png" alt="image-20221110141010814" style="zoom:50%;" />

#### 8.5.4、WATCH key [key...]

在执行multi之前，先执行watch key1 [key2]，可以监视一个（或多个）key，如果在事务**执行之前这个(或这些)key被其他命令所改动，那么事务将被打断。**



**unwatch：**取消对事务的监视

### 8.6、Redis事务三特性

1. **单独的隔离操作**
   - 事务中的所有命令都会序列化、按顺序的执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
2. **没有隔离级别的概念**
   - 队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
3. **不保证原子性**
   - 事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。



## 九、Redis持久化——RDB

### 9.1 总体介绍

Redis提供了2个不同形式的持久化方式：

- RDB
- AOF

### 9.2 RDB

#### 9.2.1 RDB是什么

在指定的==时间间隔==内将内存中的数据集==快照==写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

#### 9.2.2 备份是如何执行的

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个==临时文件替换上次持久化==好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能，如果需要进行大规模数据的恢复，且对数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。**RDB的缺点是==最后一次持久化后的数据可能丢失==**。

#### 9.2.3 Fork

- Fork的作用是复制一个与当前进程==一样的进程==。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并==作为原进程的子进程==
- 在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，处于效率考虑，Linux中引入了“==写时复制技术==”
- 一般情况父进程和子进程会共用同一段物理内存，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

#### 9.2.4 RDB持久化流程

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111110633302.png" alt="image-20221111110633302" style="zoom:67%;" />

#### 9.2.5 dump.rdb文件

在redis.conf中配置文件名称，默认为dump.rdb

![image-20221111142515578](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111142515578.png)

#### 9.2.6 配置位置

rdb文件的保存路径，也可以修改，默认为Redis启动时命令行所在的目录下 dir "/myredis/"

![image-20221111142634017](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111142634017.png)

#### 9.2.7 如何触发RDB快照；保持策略

##### 9.2.7.1 配置文件中默认的快照位置

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111143024669.png" alt="image-20221111143024669" style="zoom:67%;" />

##### 9.2.7.2 命令 save VS bgsave

**save**：save时只管保存，其他不管，全部阻塞。手动保存。不建议

==**bgsave**：Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。==

可以通过lastsave命令获取最后一次成功执行快照的时间

##### 9.2.7.3 flushall命令

执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义

##### 9.2.7.4  Save

格式：save秒钟 写操作次数

RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置符合的快照触发条件，

==默认是1分钟内改了1万次，或5分钟内改了10次，或15分钟内改了1次。==

==禁用==：==不设置save命令，或者给save传入空字符串==

##### 9.2.7.5 stop-writes-on-bgsave-error

![image-20221111143743360](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111143743360.png)

当Redis无法写入磁盘的话，直接关掉Redis的写操作，推荐yes

##### 9.2.7.6 rdbcompression压缩文件

![image-20221111143911838](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111143911838.png)

对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用==LZF算法==进行压缩。

如果你不想消耗cpu来进行压缩的话，可以设置为关闭此功能。推荐yes

##### 9.2.7.7 rdbchecksum检查完整性

![image-20221111144143359](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111144143359.png)

在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样会增加大约10%的性能消耗，吐过希望获取到最大的性能提升，可以关闭此功能。推荐yes

##### 9.2.7.8 rdb的备份

先通过config get dir查询rdb文件的目录

将*.rdb的文件拷贝到别的地方

rdb的恢复

- 关闭Redis
- 先把备份的文件拷贝到工作目录下 cp dump2.rdb dump.rdb
- 启动Redis，备份数据会直接加载

#### 9.2.8 优势

- 适合大规模的数据恢复
- 对数据完整行和一致性要求不高更适合使用
- 节省磁盘空间
- 恢复速度快

#### 9.2.9 劣势

- Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
- 虽然Redis在fork时使用了==写时拷贝技术==，但是如果数据庞大时还是比较消耗性能。
- 在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改。

#### 9.2.10 如何停止

动态停驶RDB：redis-cli config set save ""#save 后给空值，表示禁用保存策略

#### 9.2.11 小结

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111151305816.png" alt="image-20221111151305816" style="zoom:60%;" />



## 十、Redis持久化——AOF

### 10.1 AOF

#### 10.1.1 AOF是什么

==以日志的形式来记录每个写操作（增量保存）==，将Redis执行过程的所有写指令记录下来（==读操作不记录==），==只许追加文件但不可以改写文件==，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

#### 10.1.2 AOF持久化流程

（1）客户端的请求写命令会被append追加到AOF缓冲区内；

（2）AOF缓冲区根据AOF持久化策略[always,ecerysec,no]将操作sync同步到磁盘的AOF文件中；

（3）AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

（4）Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；

#### 10.1.3 AOF默认不开启

可以在redis.conf中配置文件名称，默认为==appendonly.aof==

AOF文件的保存路径，同RDB的路径一致。

#### 10.1.4 AOF和RDB同时开启，Redis听谁的？

AOF和RDB同时开启，系统默认取AOF的数据（数据不会存在丢失）

#### 10.1.5 AOF启动/修复/恢复

- AOF的备份机制和性能虽然和RDB不同，到那时备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到redis工作目录下，启动系统即加载。
- 正常恢复
  - 修改默认的appendonly no，改为yes
  - 将有数据的aof文件复制一份保存到对应目录（查看目录：confing get dir）
  - 恢复：重启redis然后重新加载
- 异常恢复
  - 修改默认的appendonly no，改为yes
  - 如遇到==AOF文件损坏==，通过/usr/loacal/bin/==redis-check-aof --fix appendonly.aof==进行恢复
  - 备份被写坏的AOF文件
  - 恢复：重启redis，然后重新加载

#### 10.1.6 AOF同步频率设置

**appendfsync always**

始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好

**appendfsync everysec**

每秒同步，每秒计入日志一次，如果宕机，本秒的数据可能丢失。

**appendfsync no**

redis不主动进行同步，把同步时机交给操作系统。

#### 10.1.7 Rewrite压缩

1. 是什么：

AOF采用文件追加方式，问价会越来越大为避免出现此种情况，新增了重写机制，当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集，可以使用命令bgrewriteaof

2. 重写原理，如何实现重写

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写（也是先写临时文件最后再rename），==redis4.0版本后的重写，是指上就是把rdb的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。==

==no-appendfsync-on-rewrite：==

如果no-appendfsync-on-rewrite=yes，不写入aof文件只写入缓存，用户请求不会阻塞，但是请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能）

如果no-appendfsync-on-rewrite=no，还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低）

触发机制，何时重写

Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且问价大于64M时触发

==重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。==

auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）

auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写

> 例如：文件达到70MB开始重写，降到50MB，下次什么时候开始重写？100MB系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size，如果Redis的==AOF当前大小>=base_size+base_size*100%==(默认)且==当前大小>=64MB==(默认)的情况下，Redis会对AOF进行重写。

3. 重写流程

（1）bgrewriteaof触发重写，判断是否当前有bgsave或bgrewriteaof在运行，如果有，则等待该命令结束后再继续执行。

（2）主进程fork出子进程执行重写操作，保证主进程不会阻塞。

（3）子进程遍历redis内存中数据到临时文件，客户端的写请求同时写入aof_buf缓冲区和aof_rewrite_buf重写缓冲区保证员AOF文件完整以及新AOF文件生成期间的新的数据修改动作不会丢失。

（4）子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。

​		  主进程把aof_rewrite_buf中的数据写入到新的AOF文件。

（5）使用新的AOF文件覆盖旧的AOF文件，完成AOF重写。

#### 10.1.8 优势

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111165539352.png" alt="image-20221111165539352" style="zoom:67%;" />

- 备份机制更稳健，丢失数据概率更低。
- 可读的日志文本，通过操作AOF稳健，可以处理误操作。

#### 10.1.9 劣势

- 比起RDB占用更多的磁盘空间。
- 恢复备份速度更慢。
- 每次读写都同步的话，有一定的性能压力。
- 存在个别bug，造成恢复不能。

#### 10.1.10 小总结

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111170027370.png" alt="image-20221111170027370" style="zoom:67%;" />

### 10.2 总结

#### 10.2.1 用哪个好？

- 官方推荐两个都启用。
- 如果对数据不敏感，可以选单独用RDB。
- 不建议单独用AOF，因为可能会出现bug。
- 如果只是做内存缓存，可以都不用。

#### 10.2.2 官网建议

- RDB持久化方式能够再指定的时间间隔能对你的数据进行快照存储。
- AOF持久化方式记录每次对服务器写的操作，当服务器重启的时候会重新执行这些命令来恢复原始的数据，AOF命令以redis协议追加保存每次写的操作到文件末尾。
- Redis还能对AOF文件进行后台重写，使得AOF文件的体积不至于过大。
- 只做缓存：如果你只希望你的数据在服务器运行的时候存在，你也可以不适用任何持久化方式。
- 同时开启两种持久化方式
- 在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。
- RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要使用AOF呢？



## 十一、主从复制

### 11.1 主从复制是什么

主机数据更新后根据配置和策略，自动同步到备机的==master/slaver机制，Master以写为，Slaver以读为主==

### 11.2 主从复制能干嘛

- 读写分离，性能扩展
- 容灾快速恢复

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111223710828.png" alt="image-20221111223710828" style="zoom:50%;" />

### 11.3 怎么玩：主从复制

拷贝多个redis.conf文件include（写绝对路径）

开启daemonize yes

Pid文件名字 pidfile

指定端口port

Log文件名字

dump.db名字 dbfilename

Appendonly 关掉或者换名字



==**搭建过程：**==

![image-20221111233324516](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111233324516.png)

#### 11.3.1 新建redis6379.conf，填写以下内容

include/myredis/redis.conf

pidfile/var/run/redis_6379.pid

port 6379

dbfilename dump6379.rdb

![image-20221111230701816](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111230701816.png)

#### 11.3.2 新建redis6380.conf，填写以下内容

![image-20221111231750071](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111231750071.png)

#### 11.3.3 新建redis6381.conf，填写以下内容

![image-20221111231814881](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111231814881.png)

==slave-priority 10==

**设置从机的优先级，值越小，优先级越高，用于选举主机时使用。默认100**

#### 11.3.4 启动三台redis服务器

![image-20221111232028131](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111232028131.png)

#### 11.3.5 查看系统进程，看看三台服务器是否启动

![image-20221111232205074](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111232205074.png)

#### 11.3.6 查看三台主机运行情况

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111232620890.png" alt="image-20221111232620890" style="zoom: 80%;" /><img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111232648194.png" alt="image-20221111232648194" style="zoom: 80%;" /><img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111232716860.png" alt="image-20221111232716860" style="zoom: 80%;" />

info replication

打印主从复制的相关消息

#### 11.3.7 配从(从)不配主(库)

slaveof <ip><port>

成为某个实例的从服务器

1. 在6380和6381上执行：slaveof 127.0.0.1 6379

![image-20221111233009756](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111233009756.png)![image-20221111233047023](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111233047023.png)

2. 在主机上写，在从机上可以读取数据

​			在从机上写数据报错

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111225717489.png" alt="image-20221111225717489" style="zoom:67%;" />

3. 主机挂掉，重启就行，一切如初

4. 从机重启需重设：slaveof 127.0.0.1 6379

   可以将配置增加到文件中。永久生效。

   <img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221111233641202.png" alt="image-20221111233641202" style="zoom:67%;" />

### 11.4 常用3招

#### 11.4.1 一主二仆

切入点问题？slave1、slave2是从头开始复制还是从切入点开始复制？比如从k4进来，那之前的k1,k2,k3是否也可以复制？

从机是否可以写？set可否？

主机shutdown后情况如何？从机是上位还是原地待命？

主机又回来了后，主机新增记录，从机还能否顺利复制？

其中一台从机down后情况如何？依照原有它能跟上大部队吗？

#### 11.4.2 薪火相传

上一个Slave可以是下一个slave的Master，Slave同样可以接收其他slaves的连接和同步请求，那么该slave作为了链条中下一个的master，可以有效减轻master的写压力，去中心化降低风险。

==用 slaveof <ip><port>==

中途变更转向：会清楚之前的数据，重新建立拷贝最新的

风险是一旦某个slave宕机，后面的slave都没法备份

主机挂了，从机还是从机，无法写数据了

#### 11.4.3 反客为主

当一个master宕机后，后面的slave可以立刻升级为master，其后面的slave不用做任何修改。

用slaveof no one 将从机变为主机。

### 11.5 复制原理

- Slave启动成功连接到master后会发送一个sync命令
- Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，以完成一次完全同步
- 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：Master继续将新的所有收集到的修改命令一次传给slave，完成同步
- 但是只要是重新连接master，一次完全同步（全量复制）将被自动执行

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112125922126.png" alt="image-20221112125922126" style="zoom:67%;" />

### 11.6 哨兵模式

#### 11.6.1 是什么？

==反客为主==的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

#### 11.6.2 怎么玩（使用步骤）

##### 11.6.2.1 调整为一主二仆模式，6379带着6380、6381

##### 11.6.2.2 自定义的/myredis 目录下新建sentinel.conf文件，名字绝对不能错

##### 11.6.2.3 配置哨兵，填写内容

sentinel monitor mymaster 127.0.0.1 6379 1

其中mymaster 为监控对象起的服务器名称，1为至少有多个哨兵同意迁移的数量。

##### 11.6.2.4 启动哨兵

/usr/loacl/bin

redis做压测可以用自带的==redis-benchmark==工具

执行redis-sentinel /myredis/sentinel.conf

![image-20221112132900993](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112132900993.png)

##### 11.6.2.5 当主机挂掉，从机选举中产生新的主机

（大概10秒左右可以看到哨兵窗口日志，切换了新的主机）

哪个从机会被选举为主机呢? 根据优先级别：slave-priority

原主机重启后会变为从机

##### 11.6.2.6 复制延时

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

#### 11.6.3 故障恢复

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112134331087.png" alt="image-20221112134331087" style="zoom:67%;" />

优先级在redis.conf中默认：replica-priority 100，值越小优先级越高

偏移量是指获得原主机数据最全的

每个redis实例启动后都会随机生成一个40位的runid

#### 11.6.4 主从复制

```java
private static JedisSentinelPool jedisSentinelPool = null;
public static Jedis getJedisFromSentinel() {
    if (jedisSentinelPool == null) {
        Set<String> sentinelSet = new HashSet<>();
        sentinelSet.add("192.168.11.103:26379");
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(10);	// 最大可用连接数
        jedisPoolConfig.setMaxIdle(5);	// 最大闲置连接数
        jedisPoolConfig.setMinIdle(5);	// 最小闲置连接数
        jedisPoolConfig.setBlockWhenExhausted(true);	// 连接耗尽是否等待
        jedisPoolConfig.setMaxWaitMillis(2000);	// 等待时间
        jedisPoolConfig.setTestOnBorrow(true);	// 取连接的时候进行一下测试 ping pong
        jedisSentinelPool = new JedisSentinelPool("mymaster", sentinelSet,jedisPoolConfig);
        return jedisSentinelPool.getResource();
    }
}
```



## 十二、Redis集群

### 12.1 问题

容量不够，redis如何进行扩容？

并发写操作，redis如何分摊？

==另外，主从模式，薪火相传模式，主机宕机，导致ip地址发送变化，应用程序中配置需要修改对应的主机地址、端口等信息。==

之前通过代理主机来解决，但是redis3.0中提供了解决方案。就是==无中心化集群==配置。

### 12.2 什么是集群

Redis集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

Redis集群通过分区（partition）来提供一定程度的可用性（availability）：即使集群中有一部分节点失效或者无法进行通讯，集群也可以继续处理命令请求。

### 12.3 删除持久化数据

将rdb，aof文件都删除掉

### 12.4 制作6个实例，6379，6380，6381，6389，6390，6391

#### 12.4.1 配置基本信息

开启daemonize yes

==Pid文件名字==

==指定端口==

Log文件名字

==Dump.rdb名字==

Appendonly关掉或者换名字

#### 12.4.2 redis cluster配置修改

cluster-enabled ==yes== 打开集群模式

cluster-config-file ==nodes-6379.conf==  设置节点配置文件名

cluster-node-timeout ==15000==  设定节点失恋时间，超过该时间（毫秒），集群自动进行主从切换

> include/home/bigdata/redis.conf
>
> port 6379
>
> pidfile "/var/run/redis_6379.pid"
>
> dbfilename "dump6379.rdb"
>
> dir "/home/bigdata/redis_cluster"
>
> logfile "/home/bigdata/redis_cluster/redis_err_6379.log"
>
> cluster-enabled yes
>
> cluster-config-file nodes-6379.conf
>
> cluster-node-timeout 15000

#### 12.4.3 修改好redis6379.conf文件，拷贝多个redis.conf文件

#### 12.4.4 使用查找替换修改另外5个文件

例如：**:%s/6379/6380**

#### 12.4.5 启动6个redis服务

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112193857010.png" alt="image-20221112193857010" style="zoom:80%;" />

### 12.5 将六个节点合成一个集群

组合之前，请==确保所有redis实例启动==后，nodes-xxx.conf文件都生成正常。

- 合体

cd /opt/redis-6.2.7/src

```
redis-cli --cluster create --cluster-replicas 1 192.168.254.130:6379
192.168.254.130:6380	192.168.254.130:6381	192.168.254.130:6389
192.168.254.130:6390	192.168.254.130:6391
```

16384 slots covered

==此处不要用127.0.0.1，请使用真实IP地址==

==--replicas 1 采用最简单的方式配置集群，一台主机，一台从机，正好三组。==



![image-20221112195220412](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112195220412.png)

![image-20221112195250613](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112195250613.png)

- 普通方式登录

可能直接进入读主机，存储数据时，会出现MOVED重定向操作。所以，应该以集群方式登录。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112195711563.png" alt="image-20221112195711563" style="zoom:80%;" />

### 12.6 -c 采用集群策略连接，设置数据会自动切换到相应的写主机

![image-20221112195845707](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112195845707.png)

### 12.7 通过cluster nodes 命令查看集群信息

![image-20221112200233787](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112200233787.png)

### 12.8 redis cluster 如何分配这六个节点？

一个集群至少要有==三个主节点==。

选项 --cluster-replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。

分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个IP地址上。

### 12.9 什么是slots

==[OK] All **16384** slots covered.==

一个Redis集群包含16384个插槽（hash slot），数据库中的每个键都属于这16384个插槽的其中一个，集群使用公式==CRC16(key)%==16384来计算键key属于哪个槽，其中CRC16(key)语句用于计算键key的CRC16校验和。

集群中的每个节点负责处理一部分插槽。举个例子，如果一个集群可以有主节点，其中：

节点A负责处理0号至5460号插槽。

节点B负责处理5461号至10922号插槽。

节点C负责处理10923号至16383号插槽。

### 12.10 在集群中录入值

在redis-cli每次录入、查询键值，redis都会计算出该key应该送往的插槽，如果不是该客户端对应服务器的插槽，redis会报错，并告知应前往的redis实例地址和端口。

redis-cli客户端提供了==-c 参数实现自动重定向==。

如**redis-cli ==-c== -p6379**登入后，再录入、查询键值对可以自动重定向。

不在一个slot下的键值，是==不能使用mget，mset等多键操作==。

![image-20221112233006101](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112233006101.png)

可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去。

![image-20221112233806630](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112233806630.png)

### 12.11 查询集群中的值

cluster getkeysinslot <slot><count> 返回count个slot槽中的键。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221112234254613.png" alt="image-20221112234254613" style="zoom:80%;" />

### 12.12 故障恢复

如果主节点下线？从节点能否自动升为主节点？注意：==15秒超时==

主节点恢复后，主从关系会如何？主节点回来变成从机。

如果所有某一段插槽的主从节点都宕掉，redis服务是否还能继续？

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage为yes，那么，整个集群都挂掉

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage为no，那么，该插槽数据全都不能使用，也无法存储。

redis.conf中的参数 cluster-require-full-coverage

### 12.13 集群的Jedis开发

即使连接的不是主机，集群会自动切换主机存储。主机写，从机读。

无中心化主从集群。无论从哪台主机写的数据，其他主机上都能读到数据。

```java
public class JedisClusterTest{
    public static void main(String[] args){
        Set<HostAndPort>set = new HashSet<HostAndPort>();
        set.add(new HostAndPort("192.168.254.130",6379));
        JedisCluster jedisCluster = new JedisCluster(set);
        jedisCluster.set("k1","v1");
        System.out.println(jedisCluster.get("k1"));
    }
}
```

### 12.14 Redis集群提供了以下好处

实现扩容

分摊压力

无中心配置相对简单

### 12.15 Redis集群的不足

多键操作时不被支持的

多键的Redis事务是不被支持的。lua脚本不被支持

由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，需要整体迁移而不是逐步过渡，复杂度较大。

## 十三、Redis应用问题解决

### 13.1 缓存穿透

#### 13.1.1 问题描述

key对应的数据再数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

![image-20221113200903155](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113200903155.png)

#### 13.1.2 解决方案

一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

解决方案：

==（1）对空值缓存：==如果一个查询返回的数据为空（不管数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟。

==（2）设置可访问的名单（白名单）：==使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。

==（3）采用布隆过滤器：==（布隆过滤器 （Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量（位图）和一系列随机映射函数（哈希函数）。

布隆过滤器可以用于检索一个元素是否存在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。）

将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

==（4）进行实时监控：==当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务。

### 13.2 缓存击穿

#### 13.2.1 问题描述

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113203131691.png" alt="image-20221113203131691" style="zoom:67%;" />

#### 13.2.2 解决方案

key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题。

解决问题：

==（1）预先设置热门数据：==在redis高峰访问之前，把一些热门数据提前存入到redis里面，加大这些热门数据key的时长

==（2）实时调整：==现场监控哪些数据热门，实时调整key的过期时长

==（3）使用锁：==

​		（1）就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

​		（2）先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex 					key

​		（3）当操作返回成功时，再进行load db的操作，并回设缓存，最后删除mutex key。

​		（4）当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113204032262.png" alt="image-20221113204032262" style="zoom:50%;" />

### 13.3 缓存雪崩

#### 13.3.1 问题描述

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key

正常访问

缓存失效瞬间

![image-20221113204652107](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113204652107.png)

#### 13.3.2 解决方案

缓存失效时的雪崩效应对底层系统的冲击非常可怕！

解决方案：

==（1）构建多级缓存架构：==nginx缓存 + redis缓存 + 其他缓存（ehcache等）

==（2）使用锁或队列：==用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况

==（3）设置过期标志更新缓存：==记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存。

==（4）将缓存失效时间分散开：==比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

### 13.4 分布式锁

#### 13.4.1 问题描述

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

分布式锁主流的实现方案：

1. 基于数据库实现分布式锁
2. 基于缓存（Redis等）
3. 基于Zookeeper

每一种分布式锁解决方案都有各自的优缺点：

1. 性能：redis最高
2. 可靠性：zookeeper最高

#### 13.4.2 解决方案：使用redis实现分布式锁

redis：命令

```
# set sku:1:info "OK" NX PX 10000
```

EX second：设置键的过期时间为second秒。SET key value EX second效果等同于setex key second value。

PX millisecond：设置键的过期时间为millisecond毫秒。set key value px millisecond效果等同于psetex key millisecond value。

NX：只在键不存在时，才对键进行设置操作。SET key value NX 效果等同于SETNX key value。

XX：只在键已经存在时，才对键进行设置操作。

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113225226939.png" alt="image-20221113225226939" style="zoom: 67%;" />

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113230039139.png" alt="image-20221113230039139" style="zoom:50%;" />

1. 多个客户端同时获取锁（setnx）

#### 13.4.3 编写代码

Redis：set num 0

```java
@GetMapping("testLock")
public void testLock() {
    // 1.获取锁，setne
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock","111");
    // 2.获取锁成功、查询num的值
    if (lock) {
        Object value = redisTemplate.opsForValue().get("num");
        // 2.1 判断num为空return
        if (StringUtils.isEmpty(value)) {
            return;
        }
        // 2.2 有值就转成int
        int num = Integer.parseInt(value+"");
        // 2.3 把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        // 2.4 释放锁, del
        redisTemplate.delete("lock");
    } else {
        // 3. 获取锁失败、每隔0.1秒在获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

重启，服务集群，通过网关压力测试：

ab -n 1000 -c 100 http://192.168.254.130:8080/test/testLock

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113231902947.png" alt="image-20221113231902947" style="zoom:67%;" />

查看redis中num的值：

![image-20221113231939924](C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113231939924.png)

#### 13.4.4 优化之设置锁的过期时间

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113232125839.png" alt="image-20221113232125839" style="zoom:67%;" />

问题：可能会释放其他服务器的锁。



场景： 如果业务逻辑的执行时间是7s。执行流程如下

1. index1业务逻辑没执行完，3秒后锁被自动释放。
2. index2获取到锁，执行业务逻辑，3秒后锁被自动释放。
3. index3获取到锁，执行业务逻辑
4. index1业务逻辑执行完成，开始调用del释放锁，这时释放的是index3的锁，倒是index3的业务只执行1s就被别人释放。

最终等于没锁的情况。

解决：setnx获取锁时，设置一个指定的唯一值（例如：uuid）；释放前获取这个值，判断是否自己的锁



<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113233130602.png" alt="image-20221113233130602" style="zoom:67%;" />

#### 13.4.5 优化之UUID防误删

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113232531014.png" alt="image-20221113232531014" style="zoom:67%;" />

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113232942606.png" alt="image-20221113232942606" style="zoom:67%;" />

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113233436602.png" alt="image-20221113233436602" style="zoom:55%;" />

问题：删除操作缺乏原子性。

场景：

1. index1执行删除时，查询到的lock值确实和uuid相等

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113233641570.png" alt="image-20221113233641570" style="zoom:67%;" />

2. index1执行删除前，lock刚好过期时间已到，被redis自动释放



<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221113234118600.png" alt="image-20221113234118600" style="zoom:50%;" />

#### 13.4.6 优化之LUA脚本保证删除的原子性

```java
@GetMapping("testLockLua")
public void testLockLua() {
    // 1.声明一个uuid，将作为一个value放入我们的key所对应的值中
    String uuid = UUID.randomUUID().toString();
    // 2.定义一个锁：lua脚本可以使用同一把锁，来实现删除！
    String skuId = "25"; // 访问skuId为25号的商品 100008348542
    String locKye = "lock:" + skuId; // 锁住的是每个商品的数据
    // 3.获取锁
    Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKye, uuid, 3, TimeUnit.SECONDS);

    // 第一种：lock与过期时间中间不写任何的代码。
    // redisTemplate.expire("lock",10,TimeUnit.SECONDS); // 设置过期时间
    // 如果true
    if (lock) {
        // 执行的业务逻辑开始
        // 获取缓存中的num数据
        Object value = redisTemplate.opsForValue().get("num");
        // 如果是空直接返回
        if (StringUtils.isEmpty(value)) {
            return;
        }
        // 不是空 如果说在这出现了异常！那么delete就删除失败！也就是说锁永远存在！
        int num = Integer.parseInt(value + "");
        // 使num每次+1放入缓存
        redisTemplate.opsForValue().set("num", String.valueOf(++num));
        /*使用lua脚本来锁*/
        // 定义lua脚本
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1] else return 0 end)";
        // 使用redis执行lua执行
        DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
        redisScript.setScriptText(script);
        // 设置以下返回值类型 为Long
        // 因为删除判断的时候，返回的0，给其封装为数据类型。如果不封装那么默认返回String类型，
        // 那么返回字符串与0会有发生错误。
        redisScript.setResultType(Long.class);
        // 第一个要是script脚本，第二个需要判断的key，第三个就是key所对应的值。
        redisTemplate.execute(redisScript, Arrays.asList(locKye), uuid)
    } else {
        // 其他线程等待
        try {
            // 睡眠
            Thread.sleep(1000);
            // 睡醒了之后，调用方法。
            testLockLua();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### 13.4.7 总结

1. 加锁

```java
// 1. 从redis中获取锁，set k1 v1 px 20000 nx
String uuid = UUID.randomUUID().toString();
Boolean lock = this.redisTemplate.opsForValue()
	.setIfAbsent("lock", uuid, 2, TimeUnit.SECONDS);
```

2. 使用lua释放锁

<img src="C:\Users\佳辉呀\AppData\Roaming\Typora\typora-user-images\image-20221114000006549.png" alt="image-20221114000006549" style="zoom:67%;" />

3. 重试

```java
Thread.sleep(500);
testLock();
```



为了确保分布式锁可用，我们至少要确保锁的实现同时**满足以下四个条件：**

- 互斥性。在任意时刻，只有一个客户端能持有锁。
- 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
- 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。
-  加锁和解锁必须具有原子性



