# Docker

## 一、介绍

**Docker的基本组成：镜像、容器、仓库**

1. **镜像：**Docker镜像就是一个只读模板。镜像可以用来创建Docker容器，一个镜像可以创建很多容器。他也相当于是一个root文件。比如官方镜像centos:7就包含了完整的一套centos:7最小系统的root文件系统。相当于容器的“源代码”，==docker镜像文件类似于Java的类模板，而docker容器实例类似于java中new出来的实例对象。==

2. **容器：**

   1. 从面向对象角度：

      Docker利用容器独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环境，==容器是用镜像创建的运行实例==。就像是Java中的类和实例对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器为镜像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器是相互隔离的、保证安全的平台。

   2. 从镜像容器角度：

      ==可以把容器看做是一个简易版的Linux环境==（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

3. **仓库：**是==集中存放镜像==文件的场所。类似于Maven仓库，存放各种jar包的地方；Docker公司提供的官方registry被称为Docker Hub，存放各种镜像模板的地方。仓库非为公开仓库(public)和私有仓库(private)两种形式。==最大的公开仓库是Docker Hub(https://hub.docker.com)，存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等。

> Docker 运行的基本流程为：
>
> 1. 用户是使用Docker Client 与 Docker Daemon建立通信，并发送请求给后者。
> 2. Docker Daemon作为 Docker架构中的主体部分，首先提供 Docker Server的功能使其可以接受Docker Client 的请求。
> 3. Docker Engine 执行Docker 内部的一系列工作，每一项工作都是以一个 Job 的形式存在。
> 4. Job 的运行过程中，当需要容器镜像是，则从 Docker Registry 中下载镜像，并通过镜像管理驱动 Grap driver 将下载镜像以 Graph 的形式存储。
> 5. 当需要为 Docker 创建网络环境时，通过网络管理驱动 Network driver 创建并配置Docker容器网络环境。
> 6. 当需要限制 Docker容器运行资源或执行用户指令等操作时，则通过 Exec driver 来完成。
> 7. Libcontainer 是一项独立的容器管理包，Network driver以及Exec driver 都是通过 Libcontainer 来实现对容器进行的操作。

## 二、安装

1. 确定你是CentOS7及以上版本  `cat /etc/redhat-release`

2. 卸载旧版本

   ````bash
   $ sudo yum remove docker \
                     docker-client \
                     docker-client-latest \
                     docker-common \
                     docker-latest \
                     docker-latest-logrotate \
                     docker-logrotate \
                     docker-engine
   ````

3. yum安装gcc相关

   ````bash
   $ sudo yum -y install gcc
   $ sudo yum -y install gcc-c++
   ````

4. 安装需要的软件包

   ````bash
   $ sudo yum install -y yum-utils
   ````

5. 设置stable镜像仓库

   ````bash
   $ sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ````

6. 更新yum软件包索引

   ````bash
   $ sudo yum makecache fast
   ````

7. 安装DOCKER CE

   ````bash
   $ sudo yum -y install docker-ce docker-ce-cli containerd.io
   ````

8. 启动docker

   ````bash
   $ sudo systemctl start docker
   查看docker版本：
   $ sudo docker version
   ````

9. 测试

   ````bash
   $ sudo docker run hello-world
   ````


10. 卸载

    ````bash
    $ sudo systemctl stop docker
    $ sudo yum remove docker-ce docker-ce-cli containerd.io
    $ sudo rm -rf /var/lib/docker
    $ sudo rm -rf /var/lib/containerd
    ````

11. 阿里云镜像加速

    1. https://promotion.aliyun.com/ntms/act/kubernetes.html 阿里云开发平台
    2. 注册一个阿里云账户
    3. 获得加速器地址链接
    4. 粘贴脚本直接执行
    5. 重启服务器

12. 在执行`docker run hello-world`的时候，run干了什么？

<img src="https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-1.png" alt="docker-1" style="zoom:67%;" />

## 三、常用命令

### 1、启动类命令

1. 帮助启动类命令：
   1. **启动docker：**==systemctl start docker==
   2. **停止docker：**==systemctl stop docker==
   3. **重启docker：**==systemctl restart docker==
   4. **查看docker状态：**==systemctl status docker==
   5. **开机启动：**==systemctl enable docker==
   6. **查看docker概要信息：**==docker info==
   7. **查看docker总体帮助文档：**==docker --help==
   8. **查看docker命令帮助文档：**==docker 具体命令 --help==

### 2、镜像命令

1. 镜像命令：

   1. **查看本地镜像：**docker images	列出本地主机上的镜像

      1. OPTIONS说明：==-a==：列出本地所有的镜像(含历史映像层)	==-q==：只显示镜像ID

   2. **查看镜像：**docker search 某个XXX镜像名字 

      1. |    参数     |       说明       |
         | :---------: | :--------------: |
         |    NAME     |     镜像名称     |
         | DESCRIPTION |     镜像说明     |
         |    STARS    |     点赞数量     |
         |  OFFICIAL   |   是否是官方的   |
         |  AUTOMATED  | 是否是自动构建的 |

      2. OPTIONS说明：==--limit==：只列出N个镜像，默认25个；  ==docker search --limit 5 redis==

   3. **下载镜像：**docker pull 某个xxx镜像名字   （下载镜像）

      1. docker pull 镜像名字[:TAG]
      2. docker pull 镜像名字  （没有TAG就是最新版，等价于docker pull镜像名字:latest）

   4. docker system df     查看镜像、容器、数据卷所占的空间

   5. **删除镜像：**docker rmi 某个xxx镜像名字ID

      1. **全部删除：**==docekr rmi -f $(docker images -qa)==

   6. 谈谈docker虚悬镜像是什么？

      1. ==是什么？==
         1. 仓库名、标签都是`<none>`的镜像，俗称虚悬镜像dangling image

### 3、容器命令

有镜像才能创建容器

1. 新建 + 启动容器：docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
   1. OPTIONS说明
      1. 有些是一个减号，有些是两个减号
      2. ==--name===“容器新名字”    为容器指定一个名称。
      3. ==-d==：后台运行容器并返回容器ID，也即启动守护式容器（后台运行）；
      4. ==-i==：以交互模式运行容器，通常与 -t 同时使用；
      5. ==-t==：为容器重新分配一个伪输入终端，通常与 -i 同时使用；
      6. 也即启动交互式容器（前台有伪终端，等待交互）
      7. ==-P==：随机端口映射，大写P
      8. ==-p==：指定端口映射，小写p
      9. | 参数                          | 说明                               |
         | ----------------------------- | ---------------------------------- |
         | -p hostPort:containerPort     | 端口映射 -p 8080:80                |
         | -p ip:hostPort:containerPort  | 配置监听地址 -p 10.0.0.100:8080:80 |
         | -p ip::containerPort          | 随机分配端口 -p 10.0.0.100:80      |
         | -p hostPort:containerPort:udp | 指定协议 -p 8080:80:tcp            |
         | -p 81:80 -p 443:443           | 指定多个                           |
   2. 启动交互式容器（前台命令行）

      1. 使用镜像centos:latest以==交互模式==启动一个容器，在容器内执行 /bin/bash 命令

         ````bash
         $sudo docker run -it centos /bin/bash
         ````

      2. /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式Shell，因此用的是 /bin/bash。

      3. 要退出终端，直接输入exit；
   3. 列出当前所有==正在运行==的容器：==docker ps [OPTIONS]==
   4. 退出容器：

      1. 两种退出方式：==exit==：run进去容器，exit退出，容器停止；==ctrl+p+q==：run进去容器，ctrl+p+q退出，容器不停止。
   5. 启动已停止运行的容器：==docker statrt 容器ID或者容器名==
   6. 重启容器：==docker restart 容器ID或者容器名==
   7. 停止容器：==docker stop 容器ID或者容器名==
   8. 强制停止容器：==docker kill 容器ID或者容器名==
   9. 删除已停止的容器：==docker rm 容器ID==

      1. 一次性删除多个容器实例：==docker rm -f $(docker ps -a -q)==
      2. docker ps -a -q|xargs docker rm

2. **重要：**(redis举例)

   1. 启动守护式容器(后台服务器)：==docker run -d redis==
   2. 查看容器日志：==docker logs 容器ID==
   3. 查看容器内运行的进程：==docker top 容器ID==
   4. 查看容器内部细节：==docker inspect 容器ID==
   5. 进入正在运行的容器并以命令行交互：
      1. ==docker exec -it 容器ID /bin/bash==
      2. 重新进入  ==docker attach 容器ID==
      3. 上述两个区别：推荐使用docker exec命令，因为退出容器终端，不会导致容器的停止。

   6. 从容器内拷贝文件到主机上：==docker cp 容器ID:容器内路径 目的主机路径==
      1. 例：docker cp 681e777e7aa2:/tmp/a.txt /etc

   7. 导入和导出容器
      1. export 导出容器的内容流作为一个tar归档文件[对应import命令]
      2. import从tar包中的内容创建一个新的文件系统再导入为镜像[对应export]
      3. 案例：
         1. docker export 容器ID > 文件名.tar
         2. cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号


## 四、Docker镜像

### **镜像：**

是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。

只有通过这个镜像文件才能生成Docker容器实例（类似Java中new出来的一个对象）。

### 分层的镜像

镜像是分层的



### **UnionFS（联合文件系统）**：

Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持==对文件系统的修改作为一次提交来一层层的叠加==，同时可以将不同目录挂载到同一个虚拟文件系统下（unite several directories into a single virtual filesystem）。Union文件系统是Docker镜像的基础。**镜像可以通过分层来进行继承**，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的形成一个对外暴露的服务实体。



### **Docker镜像加载原理：**

![docker-2](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-2.png)

![docker-3](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-3.png)

### 为什么Docker镜像要采用这种分层结构呢？

镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了复用。

比如说有多个镜像都从相同的base镜像构建而来，那么Docker Host只需在磁盘上保存一份base镜像；同时内存中也只需加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。

**==Docker 镜像层都是只读的，容器层是可写的==**，当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作“容器层”，“容器层”之下的都叫“镜像层”。

### commint

docker commin -m="提交的描述信息" -a="作者"  容器ID要创建的目标镜像名：[标签名]

**案例演示ubuntu安装vim：**

1. 从Hub上下载ubuntu镜像到本地并成功运行。

   ```bash
   # 启动ubuntu
   $ docker run -it ubuntu /bin/bash
   ```

2. 原始默认Ubuntu镜像是不带着vim命令的

3. 外网连通的情况下，按装vim

   ```bash
   # 先更新我们的包管理工具
   $sudo apt-get update
   # 然后安装我们需要的vim
   $sudo apt-get -y install vim
   ```

4. 安装完成后，commit我们自己的新镜像

5. 启动我们的新镜像并和原来的对比

**小总结：**

Docker中的镜像分层，==支持通过扩展现有镜像，创建新的镜像==。类似Java继承于一个Base基础类，自己再按需扩展。新镜像是从base镜像一层一层叠加生成的。每安装一个软件，就在现有镜像的基础上增加一层。

### 本地镜像发布到阿里云

```bash
# 登录阿里云Docker Registry
$ docker login --username=jhsocool registry.cn-hangzhou.aliyuncs.com
# 将镜像推送到Registry
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/mxgdzh/myubuntu1.3:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/mxgdzh/myubuntu1.3:[镜像版本号]
# 从Registry中拉取镜像
$ docker pull registry.cn-hangzhou.aliyuncs.com/mxgdzh/myubuntu1.3:[镜像版本号]
```

### 本地镜像推送到私有库

1. 下载镜像Docker Registry

   ```bash
   $ docker pull registry
   ```

2. 运行私有库Registry，相当于本地有个私有Docker hub

   ```bash
   $ docker run -d -p 5000:5000 -v /zzyyuse/myregistry/:/tmp/registry --privileged=true registry
   ```

3. 案例演示创建一个新镜像，ubuntu安装 ifconfig 命令

   1. 从Hub上下载ubuntu镜像到本地并成功运行

   2. 原始的ubuntu镜像是不带着 ifconfig 命令的

   3. 外网连通的情况下，安装 ifconfig 命令并测试通过

      ```bash
      $ apt-get update
      $ apt-get install net-tools
      ```

   4. 安装完成后，commit 我们自己的新镜像

      ```bash
      # 公式：
      $ docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]
      # 命令：再容器外执行
      ```

   5. 启动我们的新镜像并和原来的对比

4. curl验证私服库上有什么镜像

   ```bash
   $ curl -XGET http://192.168.2.129:5000/v2/_catalog
   ```

5. 将新镜像 [镜像名] 修改符合私服规范的Tag

   ```bash
   # 按照公式：docker tag 镜像：Tag Host:Port/Repository:Tag
   # 自己host主机IP地址，填写自己的
   # 使用命令docker tag将dzhubuntu:1.2这个镜像修改为192.168.111.162:5000/dzhubuntu:1.2
   
   $ docker tag dzhubuntu:1.2 192.168.2.129:5000/dzhubuntu:1.2
   ```

6. 修改配置文件使之支持http

   ```bash
   # 查看
   $ cat /etc/docker/daemon.json
   # 编辑
   $ vim /etc/docker/daemon.json
   {
   	"registry-mirrors":["https://aa25ingu.mirror.aliyuncs.com"],
   	"insecure-registries":["192.168.2.129:5000"]
   }
   # "insecure-registries":["自己虚拟机的ip:5000"]
   
   # docker默认不允许http方式推送镜像，通过配置选项来取消这个限制。--> 修改完后如果不生效，建议重启docker
   ```

7. push推送到私服库

   ```bash
   $ docker push 192.168.2.129:5000/ifconfigubuntu:1.2
   ```

8. curl验证私服库上有什么镜像

   ```bash
   $ curl -XGET http://192.168.2.129:5000/v2/_catalog
   ```

9. pull到本地并运行

   ```bash
   $ docker pull 192.168.2.129:5000/ifconfigubuntu:1.2
   ```



## 五、Docker容器数据卷

**容器卷记得加入**：==--privileged=true==

>Docker挂载主机目录访问如果出现cannot open directory .: Permission denied
>
>解决办法：在挂载目录后多加一个==--privileged=true==参数即可
>
>如果是CentOS7安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为，在SELinux里面挂载目录被禁止掉了，如果要开启，我们一般使用--privileged=true命令，扩大容器的权限解决挂载目录没有权限的问题，也即使用改参数，container内的root拥有真正的root权限，否则，container内的root只是外部的一个普通用户权限。



卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性：

卷的设计目的就是==数据的持久化==，完全独立于容器的生存周期，因此Docker不会再容器删除其挂载的数据卷

**数据卷能干吗？**

将运用与运行的环境打包镜像，run后形成容器实例运行，但是我们对数据的要求希望是==持久化的==

Docker容器产生的数据，如果部备份，那么当容器实例删除后，容器内的数据自然也就没有了。

为了能保存数据再docker中我们使用卷。

特点：

1. 数据卷可在容器之间共享或重用数据
2. 卷中的更改可以直接实时生效
3. 数据卷中的更改不会包含再镜像的更新中
4. 数据卷的生命周期一致持续到没有容器使用它为止

### 一、宿主vs容器之间映射添加容器卷

1. 直接命令添加

   1. 命令：==docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名==

   2. 查看数据卷是否挂载成功

      ```bash
      $ docker inspect 容器ID
      ```

   3. 容器和宿主机之间数据共享

      1. docker修改，主机同步获得
      2. 主机修改，docker同步获得
      3. docker容器stop，主机修改，docker容器重启看数据是否同步。

### 二、读写规则映射添加说明

1. 读写(默认)
   1. ==docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名==
   2. 默认同上案例，默认就是rw
2. 只读
   1. 容器实例内部被限制，只能读取不能写
   2. ==docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名==

### 三、卷的继承和共享

1. 容器1完成和宿主机的映射

   ```bash
   $ docker run -it --privileged=true -v /mydocker/u:/tmp/u --name u1 ubuntu
   ```

2. 容器2继承容器1的卷规则

   ```bash
   $ docker run -it --privileged=true --volumes-from 父类 --name u2 ubuntu
   ```



## 六、Docker常规安装简介

### 1、总体步骤

1. 搜索镜像
2. 拉去镜像
3. 查看镜像
4. 启动镜像
   1. 服务端口映射
5. 停止容器
6. 移除容器

### 2、安装tomcat

```bash
$sudo docker pull tomcat
```

==docker run -it -p 8080:8080 tomcat==

​	-p 小写，主机端口：docker容器端口

​	-P 大写，随机分配端口

​	i：交互

​	t：终端

​	d：后台

访问tomcat首页出现404问题：

1. 可能没有映射端口或者没有关闭防火墙
2. 把webapps.dist目录换成webapps
   1. 进入终端：docker exec -it 实例ID /bin/bash
   2. 删除webapps文件：rm -f webapps
   3. 将webapps.dist文件重命名为webapps：mv webapps.dist webapps

免修改版说明(tomcat8)：

```bash
$sudo docker pull billygoo/tomcat8-jdk8
$sudo docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8
```

### 3、安装mysql

**简单版：**

```bash
$sudo docker pull mysql:5.7
```

==docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7==

问题：

1. 插入中文数据报错，乱码；原因：docker上默认字符集编码隐患，解决：在mysql容器中执行==SHOW VARIABLES LIKE 'character%'==查看；修改my.cnf文件
2. 删除容器后，里面的mysql数据怎么办？

**实战版：**

1. 新建mysql容器实例

```bash
$sudo docker run -d -p 3306:3306 --privileged=true -v /zzyyuse/mysql/log:/var/log/mysql -v /zzyyuse/mysql/data:/var/lib/mysql -v /zzyyuse/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7 
```

2. 新建my.cnf

   1. 通过容器卷同步给mysql容器实例

   ```bash
   vim my.cnf
   # 再my.cnf文件中添加：
   [client]
   default_character_set=utf8
   [mysqlld]
   collation_server=utf8_general_ci
   character_set_server=utf8
   # 查看my.cnf文件
   cat my.cnf
   ```

   

3. 重新启动mysql容器实例在重新进入并查看字符编码

4. 再新建库新建表再插入中文测试

5. 结论

<img src="https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-4.png" alt="docker-4" style="zoom:67%;" />

### 4、安装redis

```bash
$sudo docker run -d -p 6379:6379 redis:6.0.8
```

启动redis

```bash
$sudo docker run -p 6379:6379 --name myr3 --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data-d redis:6.0.8 redis-server /etc/redis/redis.conf
```



## 七、Dokcer复杂安装详说

### 1、安装mysql主从复制

**主从复制原理**

**主从搭建步骤**：

1. ==新建主服务器容器实例3307==

   ```bash
   $sudo docker run -p 3307:3306 --name mysql-master \
   -v /mydata/mysql-master/log:/var/log/mysql \
   -v /mydata/mysql-master/data:/var/lib/mysql \
   -v /mydata/mysql-msater/conf:/etc/mysql/conf.d \
   -e MYSQL_ROOT_PASSWORD=root \
   -d mysql:5.7
   ```

2. 进入/mydata/mysql-master/conf目录下新建my.cnf

   ```bash
   $sudo vim my.cnf
   ```

   ```bash
   # 在my.cnf文件中插入以下内容
   
   [mysqld]
   ## 设置server_id,同一局域网中需要唯一
   server_id=101
   ## 指定不需要同步的数据库名称
   binlog-ignore-db=mysql
   ## 开启二进制日志功能
   log-bin=mall-mysql-bin
   ## 设置二进制日志使用内存大小（事务）
   binlog_cache_size=1M
   ## 设置使用的二进制日志格式（mixed,statement,row）
   binlog_format=mixed
   ## 二进制日志过期清理时间。默认值为0，表示不自动清理。
   expire_logs_days=7
   ## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
   ## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
   slave_skip_errors=1062
   ```

3. 修改完配置后重启master实例

   ```bash
   $sudo docker restart mysql-master
   ```

4. 进入mysql-master容器

   ```bash
   $sudo docker exec -it mysql-master /bin/bash
   $sudo mysql -uroot -proot
   ```

5. master容器实例内创建数据同步用户

   ```bash
   $sudo create user 'slave'@'%' identified by '123456';
   $sudo GRANT REPLICATION SLAVE,REPLICATION CLIENT ON *.* TO 'slave'@'%';
   ```

6. ==新建从服务器容器实例3308==

   ```bash
   $sudo docker run -p 3308:3306 --name mysql-slave \
   -v /mydata/mysql-slave/log:/var/log/mysql \
   -v /mydata/mysql-slave/data:/var/lib/mysql \
   -v /mydata/mysql-slave/conf:/etc/mysql/conf.d \
   -e MYSQL_ROOT_PASSWORD=root \
   -d mysql:5.7
   ```

7. 进入/mydata/mysql-slave/conf目录下新建my.cnf

   ```bash
   $sudo vim my.cnf
   ```

   ```bash
   # 在my.cnf文件中插入以下内容
   
   [mysqld]
   ## 设置server_id,同一局域网中需要唯一
   server_id=102
   ## 指定不需要同步的数据库名称
   binlog-ignore-db=mysql
   ## 开启二进制日志功能
   log-bin=mall-mysql-slave1-bin
   ## 设置二进制日志使用内存大小（事务）
   binlog_cache_size=1M
   ## 设置使用的二进制日志格式（mixed,statement,row）
   binlog_format=mixed
   ## 二进制日志过期清理时间。默认值为0，表示不自动清理。
   expire_logs_days=7
   ## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
   ## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
   slave_skip_errors=1062
   ## relay_log配置中继日志
   relay_log=mall-mysql-relay-bin
   ## log_slave_updates表示slave将复制事件写进自己的二进制日志
   log_slave_updates=1
   ## slave设置为只读（具有super权限的用户除外）
   read_only=1
   ```

8. 修改完配置后重启slave实例

   ```bash
   $sudo docker restart mysql-slave
   ```

9. 在主数据库中查看主从同步状态

   ```mysql
   mysql> show master status;
   ```

10. 进入mysql-slave容器

    ```bash
    $sudo docker exec -it mysql-master /bin/bash
    $sudo mysql -uroot -proot
    ```

11. 在从数据库中配置主从复制

    ```bash
    $sudo change master to master_host='宿主机ip',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=617,master_connect_retry=30;
    ```

    **主从复制命令参数说明：**

    master_host：主数据库的IP地址；

    master_port：主数据库的运行端口；

    master_user：在主数据库创建的用于同步数据的用户账号；

    master_password：在主数据库创建的用于同步数据的用户密码；

    master_log_file：指定从数据库要复制数据的日志文件，通过查看主数据的状态，获取File参数；

    master_log_pos：指定从数据库从哪个位置开始复制数据，通过查看主数据的状态，获取Position参数；

    master_connect_retry：连接失败重试的时间间隔，单位为秒。

12. 在从数据库中查看主从同步状态

    ```mysql
    mysql> show slave status \G;
    ```

    ![docker-5](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-5.png)

13. 在从数据库中开启主从同步

    ```mysql
    mysql> start slave;
    ```

    ![docker-6](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-6.png)

14. 查看从数据库状态发现已经同步

    ```mysql
    mysql> show slave status \G;
    ```

    ![docker-7](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-7.png)

15. 主从复制测试

    1. 主机新建库——使用库——新建表——插入数据，ok
    2. 从机使用库——查看记录，ok

### 2、安装redis集群

#### 一、3主3从redis集群配置

1. 关闭防火墙，启动docker后台服务

2. 新建6个docker容器实例

   ```bash
   $sudo docker run -d --name redis-node-1 --net host --privileged=true -v /data/redis/share/redis-node-1:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6381
   $sudo docker run -d --name redis-node-2 --net host --privileged=true -v /data/redis/share/redis-node-2:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6382
   $sudo docker run -d --name redis-node-3 --net host --privileged=true -v /data/redis/share/redis-node-3:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6383
   $sudo docker run -d --name redis-node-4 --net host --privileged=true -v /data/redis/share/redis-node-4:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6384
   $sudo docker run -d --name redis-node-5 --net host --privileged=true -v /data/redis/share/redis-node-5:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6385
   $sudo docker run -d --name redis-node-6 --net host --privileged=true -v /data/redis/share/redis-node-6:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6386
   ```

   > docker run   创建并运行docker容器实例
   >
   > --name redis-node-6    容器名字
   >
   > --net host      使用宿主机的IP和端口，默认
   >
   > --privileged=true	获取宿主机root用户权限
   >
   > -v /data/redis/share/redis-node-6:/data	容器卷，宿主机地址：docker内部地址
   >
   > redis:6.0.8	redis镜像和版本号
   >
   > --cluster-enabled yes	开启redis集群
   >
   > --appendonly yes	开启持久化
   >
   > --port 6386	redis端口号

3. 进入容器redis-node-1并为6台机器构建集群关系

   1. 进入容器 

      ```bash
      $sudo docker exec -it redis-node-1 /bin/bash
      ```

   2. 构建主从关系

      ```bash
      ## 注意，进入docker容器后才能执行以下命令，且注意自己的真实IP地址
      ## redis-cli --cluster create IP地址:6381 IP地址:6382 IP地址:6383 IP地址:6384 IP地址:6385 IP地址:6386 --cluster-replicas 1
      redis-cli --cluster create 192.168.2.129:6381 192.168.2.129:6382 192.168.2.129:6383 192.168.2.129:6384 192.168.2.129:6385 192.168.2.129:6386 --cluster-replicas 1
      ## --cluster-replicas 1 表示为每个master创建一个slave节点
      ```

   3. 一切OK的话，3主3从搞定

4. 链接进入6381作为切入点，查看集群状态

   1. 链接进入6381作为切入点，查看节点状态
   2. cluster info
   3. cluster nodes

#### 二、主从容错切换迁移案例

1. 数据读写存储

   1. 启动6机 构成的集群并通过exec进入
   2. 对6381新增两个key
   3. 防止路由失效加参数 -c 并新增两个key   `redis-cli -p 6381 -c`
   4. 查看集群信息   `redis-cli --cluster check 192.168.2.129:6381`

2. 容错切换迁移

   1. 主6381和从机切换，先停止主机6381

   2. 再次查看集群信息

   3. 先还原之前的3主3从

      1. 先启6381	`docker start redis-node-1`
      2. 再停6384    `docker stop redis-node-5`
      3. 再启6384    `docker start redis-node-5`

   4. 查看集群状态

      ```bash
      $sudo redis-cli --cluster check 自己IP:6381
      ```

#### 三、主从扩容案例

1. 新建6387、6388两个节点 + 新建后启动 + 查看是否8节点

   ```bash
   $sudo docker run -d --name redis-node-7 --net host --privileged=true -v /data/redis/share/redis-node-7:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6387
   
   $sudo docker run -d --name redis-node-8 --net host --privileged=true -v /data/redis/share/redis-node-8:/data redis:6.0.8 --cluster-enabled yes --appendonly yes --port 6388
   ```

2. 进入6387容器实例内部

   ```bash
   $sudo docker exec -it redis-node-7 /bin/bash
   ```

3. 将新增的6387节点（空槽号）作为master节点加入原集群

   ```mysql
   ## 将新增的6387作为master节点加入集群
   redis-cli --cluster add-node 自己实际IP地址:6387 自己实际IP地址:6381
   ## 6387就是将要作为master新增节点
   ## 6381就是原来集群节点里面的领路人，相当于6387拜拜6381的码头从而找到组织加入集群
   ```

4. 检查集群情况第1次

   ```bash
   $sudo redis-cli --cluster check 真实IP地址:6381
   ```

5. 重新分派槽号

   ```bash
   ## 命令： redis-cli --cluster reshard IP地址:端口号
   $sudo redis-cli --cluster reshard 192.168.2.129:6381
   ```

6. 检查集群情况第2次

   ```bash
   $sudo redis-cli --cluster check 真实IP地址:6381
   ```

   > 重新分配的槽号是3个主每家匀一点给6387，而非4个主全部重新分配。全部重新分配成本太大

7. 为主节点6387分配从节点6388

   ```bash
   ## 命令： redis-cli --cluster add-node ip地址:新slave端口 ip地址:新master端口 --cluster-slave --cluster-master-id 新主机节点ID
   redis-cli --cluster add-node 192.168.2.129:6388 192.168.2.129:6387 --cluster-slave --cluster-master-id 39f9ef0d844274060d2d4d1df9593c7fcb4c41a3   ## 这个是6387的编号
   ```

8. 检查集群情况第3次

   ```bash
   $sudo redis-cli --cluster check 真实IP地址:6381
   ```

#### 四、主从缩容案例

删除6387和6388，恢复3主3从

1. 目的：6387和6388下线

2. 检查集群情况1获得6388的节点ID

   ```bash
   $sudo redis-cli --cluster check 192.168.2.129:6381
   # 9a71bf697a0d57fe5a008a59c5ee611e6f67d39b
   ```

3. 将6388删除，从集群中将4号从节点6388删除

   ```bash
   # 命令：redis-cli --cluster del-node IP地址:端口号 从机6388节点ID
   $sudo redis-cli --cluster del-node 192.168.2.129:6388 9a71bf697a0d57fe5a008a59c5ee611e6f67d39b
   ```

4. 将6387的槽号清空，重新分配，本例将清出来的槽号都给6381

   ```bash
   $sudo redis-cli --cluster reshard 192.168.2.129:6381
   ```

   <img src="https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-8.png" alt="docker-8" style="zoom:67%;" />

5. 检查集群情况第二次

   ```bash
   $sudo redis-cli --cluster check IP地址:6381
   ```

6. 将6387删除

   ```bash
   # 命令：redis-cli --cluster del-node IP地址:从机端口 6387节点ID
   $sudo redis-cli --cluster del-node 192.168.2.129:6387 39f9ef0d844274060d2d4d1df9593c7fcb4c41a3
   ```

7. 检查集群情况第三次

   ```bash
   $sudo redis-cli --cluster check 192.168.2.129:6381
   ```

## 八、DockerFile解析

### 1、是什么

Dockerfile是用来构建Docker镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

官网：https://docs.docker.com/engine/reference/builder/

### 2、DockerFile构建过程解析

1. Dockerfile内容基础知识
   1. 每条保留字指令都==必须为大写字母==且后面要跟随至少一个参数
   2. 指令按照从上到下，顺序执行
   3. #表示注释
   4. 每条指令都会创建一个新的镜像层并对镜像进行提交
2. Docker执行Dockerfile的大致流程
   1. docker从基础镜像运行一个容器
   2. 执行一条指令并对容器做出修改
   3. 执行类似docker commit的操作提交也给新的镜像层
   4. docker再基于刚提交的镜像运行一个新容器
   5. 执行dockerfile中的下一条指令制导所有指令都执行完成

![docker-9](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-9.png)

![docker-9.5](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-9.5.png)

### 3、DockerFile常用保留字指令

参考tomcat8的dockerfile入门——https://github.com/docker-library/tomcat

1. FROM：基础镜像，当前镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板，第一条必须是from

2. MAINTAINER：镜像维护者的姓名和邮箱地址

3. RUN：

   1. 容器构建时需要运行的命令

   2. 两种格式

      1. shell 格式

         ```shell
         RUN <命令行命令>
         # <命令行命令> 等同于，在终端操作的 shell命令
         ```

      2. exec 格式

         ```shell
         RUN ["可执行文件","参数1","参数2"]
         # 例如：
         # RUN ["./test.php","dev","offline"] 等价于 RUN ./test.php dev offline
         ```

   3. RUN是在 docker bulid 时运行

4. EXPOSE：当前容器对外暴露出的端口

5. WORKDIR：指定在创建容器后，终端默认登录的进来工作目录，一个落脚点

6. USER：指定该镜像以什么样的用户去执行，如果都不指定，默认是root

7. ENV：用来在构建镜像过程中设置环境变量

8. ADD：将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包

9. COPY：

   1. 类似ADD，拷贝文件和目录到镜像中。

   2. 将从构建上下文目录中<源路径>的文件 / 目录复制到新的一层的镜像内的<目标路径>位置。

      ```txt
      COPY src dest
      COPY ["src","dest"]
      <src源路径>: 源文件或者源目录
      <dest目标路径>：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动建好
      ```

10. VOLUME：容器数据卷，用于数据保存和持久化工作

11. CMD:

    1. 指定容器启动后的要干的事情

       CMD容器启动命令

       `CMD`指令的格式和`RUN`相似，也是两种格式：

       - `shell`格式：`CMD <命令>`
       - `exec`格式：`CMD ["可执行文件"， "参数1", "参数2"...]
       - 参数列表格式：`CMD ["参数1"，"参数2"...]`。在指定了`ENTRYPOINT`指令后，用`CMD`指定具体的参数。

    2. 注意

       1. Dockerfile中可以有多个CMD指令，==但只有最后一个生效，CMD会被 docker run 之后的参数替换==
       2. 参考官网Tomcat的dockerfile演示讲解

    3. 它和前面RUN命令的区别

       1. CMD是在 docker run 时运行。
       2. RUN是在 docker build 时运行。

12. ENTRYPOINT

    1. 也是用来指定一个容器启动时要运行的命令

    2. 类似于CMD指令，但是ENTRYPOINT==不会被docker run后面的命令覆盖==，==而且这些命令行参数会被当做参数送给ENTRYPOINT指令指定的程序==

    3. 命令格式和案例说明

       ![docker-10](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-10.png)

    4. 优点：在执行 docker run 的时候可以指定 ENTRYPOINT　运行所需的参数。

    5. 注意：如果 Dockerfile中如果存在多个 ENTRYPOINT 指令，仅最后一个生效。

### 4、案例

#### 自定义镜像mycentosjava8

1. 要求

   1. Centos7镜像具备vim+ifconfig+jdk8
   2. JDK的下载镜像地址
      1. 官网
      2. https://mirrors.yangxingzhen.com/jdk/

2. 编写

   1. 准备编写Dockerfile文件，大写字母D

3. 构建

   1. docker build -t 新镜像名字:TAG .
   2. 注意：上面TAG后面有个空格，有个点

4. 运行

   ```bash
   $sudo docker run -it 新镜像名字:TAG
   ```

5. 再体会下UnionFS（联合文件系统）

#### 虚悬镜像

1. 是什么

   1. 仓库名、标签都是<none>的镜像，俗称dangling image

   2. Dockerfile写一个

      ![docker-11](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-11.png)

2. 查看

   1. docker images ls -f dangling=true

   2. 命令结果

      ![docker-12](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-12.png)

3. 删除

   ```bash
   $sudo docker image prune
   ```

## 九、Docker微服务实战

### 1、通过IDEA新建一个普通微服务模块

1. 建Module
2. 该POM
3. 写YML
4. 主启动
5. 业务类

### 2、通过dockerfile发布微服务部署到docker容器

1. IDEA工具里面搞定微服务jar包

2. 编写Dockerfile

   1. Dockerfile内容
   2. 将微服务jar包和Dockerfiel文件上传到同一个目录下 /mydocker

3. 构建镜像

   1. docker build -t dzh_docker:1.6 .
   2. 打包成镜像文件

4. 运行容器

   docker run -d -p 6001:6001 dzh_docker:1.6

5. 访问测试

## 十、Docker网络

### 1、是什么

1. docker不启动，默认网络情况

   1. ens33
   2. lo
   3. virbr0

2. docker启动后，网络情况

   1. 查看docker网络模式命令

      ```bash
      $sudo docker network ls
      ```

### 2、常用基本命令

1. ALL命令
2. 查看网络：docker network ls
3. 查看网络源数据：docker network inspect XXX网络名字
4. 删除网络：docker network rm XXX网络名字

### 3、能干嘛

1. 容器间的互联和通信一级端口映射
2. 容器IP变动的时候可以通过服务名直接网络通信而不受到影响

### 4、网络模式

1. 总体介绍

   | 网络模式  |                             简介                             |
   | :-------: | :----------------------------------------------------------: |
   |  bridge   | 为每一个容器分配、设置IP等，并将容器连接到一个`docker0`，虚拟网桥，默认为该模式。 |
   |   host    | 容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。 |
   |   none    | 容器有独立的 Network namespace，但并没有对其进行任何网络设置，如分配 veth pair 和网桥连接，IP等。 |
   | container | 新创建的容器不会创建自己的网卡和配置自己的IP，二十和一个指定的容器共享IP，端口范围 |

   1. bridge模式：使用 --network bridge 指定，默认使用docker0
   2. host模式：使用 --network host 指定
   3. none模式：使用 --network none 指定
   4. container模式：使用 --network container:NAME 或者容器ID指定

2. 容器实例内默认网络IP生产规则

   1. 说明

      <img src="https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-13.png" alt="docker-13" style="zoom:67%;" />

   2. 结论：docker容器内部的ip是有可能会发生变化的

3. 案例说明

   1. bridge：

      1. 是什么？

         ![docker-14](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-14.png)

      2. 案例：

         1. 说明：

            ![docker-15](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-15.png)

            ![docker-16](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-16.png)

         2. 代码：

            ```bash
            $sudo docker run -d -p 8081:8080 --name tomcat81 billygoo/tomcat8-jdk8
            $sudo docker run -d -p 8082:8080 --name tomcat82 billygoo/tomcat8-jdk8
            ```

         3. 两两匹配验证

      3. host：

         1. 是什么：直接使用宿主机的IP地址与外界进行通信，不再需要额外进行NAT转换。

         2. 案例：

            1. 说明：

               ![docker-17](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-17.png)

            2. 代码

               1. 警告：

                  ```bash
                  $sudo docker run -d -p 8083:8080 --network host --name tomcat83 billygoo/tomcat8-jdk8
                  ```

                  ![docker-18](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-18.png)

               2. 正确

                  ```bash
                  $sudo docker run -d --network host --name tomcat83 billygoo/tomcat8-jdk8
                  ```

            3. 无之前的配对显示了，看容器实例内部

               <img src="https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-19.png" alt="docker-19" style="zoom:67%;" />

            4. 没有设置 -p 的端口映射了，如何访问启动的tomcat83？？

               1. http://宿主机IP:8080/
               2. 在CentOS里面默认的火狐浏览器访问容器内的tomcat83看到访问成功，因为此时容器的IP借用主机的，所以容器共享宿主机网络IP，这样的好处是外部主机与容器可以直接通信。

      4. none:

         1. 是什么：禁用网络功能，只有lo表示（就是127.0.0.1表示本地回环）

            1. 在none模式下，并不为Docker容器进行任何网络配置。也就是说，这个Docker容器没有网卡、IP、路由等信息，只有一个lo需要我们自己为Docker容器添加网卡、配置IP等。

         2. 案例：

            ```bash
            $sudo docker run -d -p 8084:8080 --network none --name tomcat84 billygoo/tomcat8-jdk8
            ```

      5. container

         1. 是什么：

            ![docker-20](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-20.png)

         2. 错误案例：

            ```bash
            $sudo docker run -d -p 8085:8080 --name tomcat85 billygoo/tomcat8-jdk8
            $sudo docker run -d -p 8086:8080 --network container:tomacat85 --name tomcat86 billygoo/tomcat8-jdk8
            ```

            1. 运行结果：

               ![docker-21](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-21.png)

         3. 正确案例：

            1. Alpine操作系统是一个面向安全的轻型Linux发行版

               ![docker-22](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-22.png)

            2. ```bash
               $sudo docker run -it --name alpine1 alpine /bin/sh
               $sudo docker run -it --network container:alpine1 --name alpine2 alpine /bin/sh
               ```

            3. 运行结果，验证共用搭桥

            4. 假如此时关闭alpine1，在看看alpine2

      6. 自定义网络
      
         1. 过时的link
         2. 是什么
         3. 案例
            1. before
            2. after
      


### 5、Docker平台架构图解

## 十一、Docker-compose容器编排

 ### 1、是什么

Docker-Compose是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。

可以管理多个Docker容器组成一个应用。需要定义一个YAML格式的配置文件docker-compose.yml，==写好多个容器之间的调用关系==。然后，只要一个命令，就能同时启动、关闭这些容器。

### 2、能干吗

docker建议我们每一个容器中只运行一个服务，因为docker容器本身占用资源极少，所以最好是将每个服务单独的分割开来但是这样我们又面临了一个问题？

如果我需要同时部署好多个服务，难道要每个服务单独写Dockerfile然后再构建镜像，构建容器？所以dockergf提供了docker-compose多个服务部署的工具。

例如要实现一个Web微服务项目，除了Web服务容器本身，往往还需要再加上后端的数据库mysql服务容器，redis服务器，注册中心eureka，甚至还包括负载均衡容器等。。

Compose允许用户通过一个单独的docker-compose.yml模板文件（YMAL格式）来定义一组相关联的应用容器为一个项目。

可以很容易地用一个配置文件定义一个多容器的应用，然后使用一条指令安装这个应用的所有依赖，完成构建。Docker-Compose解决了容器与容器之间如何管理编排的问题。

### 3、去哪下

1. 官网：https://docs.docker.com/compose/compose-file/compose-file-v3/

2. 官网下载：https://docs.docker.com/compose/install/

3. 安装步骤：

   ```bash
   $sudo curl -SL https://github.com/docker/compose/releases/download/v2.19.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
   $sudo chmod +x /usr/local/bin/docker-compose
   $sudo docker compose version
   ```

4. 卸载步骤

   ```bash
   $sudo rm /usr/local/bin/docker-compose
   ```

### 4、Compose核心概念

1. 一个文件：docekr-compose.yml
2. 两个要素：
   1. 服务（service）：一个个应用容器实例，比如订单微服务、库存微服务、mysql容器、nginx容器或者redis容器
   2. 工程（project）：由一组关联的应用容器组成的一个==完整业务单元==，再docker-compose.yml文件中定义。
   3. 工程 == 多个服务

### 5、Compose使用的三个步骤

1. 编写Dockerfile定义各个微服务应用并构建出对应的镜像文件
2. 使用docker-compose.yml定义一个完整业务单元，安排好整体应用中的各个容器服务。
3. 最后，执行docker-compose up命令来启动并运行整个应用程序，完成一键部署上线

### 6、Compose常用命令

| 命令                                | 名称                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| docker-compose -h                   | # 查看帮助                                                   |
| docker-compose up                   | # 启动所有docker-compose服务                                 |
| docker-compose up -d                | # 启动所有docker-compose服务并后台运行                       |
| docker-compose down                 | # 停止并删除容器、网络、卷、镜像                             |
| docker-compose exec yml里面的服务id | # 进入容器实例内部 docker-compose exec docker-compose.yml文件中写的服务id /bin/bash |
| docker-compose ps                   | # 展示当前docker-compose编排过的运行的所有容器               |
| docker-compose top                  | # 展示当前docker-compose编排过的容器进程                     |
| docker-compose logs yml里面的服务id | # 查看容器输出日志                                           |
| docker-compose config               | # 检查配置                                                   |
| docker-compose config -q            | # 检查配置，有问题才输出                                     |
| docker-compose restart              | # 重启服务                                                   |
| docker-compose start                | # 启动服务                                                   |
| docker-compose stop                 | # 停止服务                                                   |

### 7、Compose编排微服务

#### 1. 改造升级微服务工程docker_boot

1. 以前的基础版

2. SQL建表建库

   ```sql
   CREATE TABLE `t_user` (
   	`id` int(10) UNSIGNED not null auto_increment,
   	`username` VARCHAR(50) not null default '' comment '用户名',
   	`password` varchar(50) not null default '' comment '密码',
   	`sex` TINYINT(4) not null default '0' COMMENT '性别 0=女 1=男',
   	`deleted` TINYINT(4) UNSIGNED not null default '0' COMMENT '删除标志，默认0不删除，1删除',
   	`update_time` TIMESTAMP not null DEFAULT CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP comment '更新时间',
   	`create_time` TIMESTAMP not null default CURRENT_TIMESTAMP COMMENT '创建时间',
   	PRIMARY KEY (`id`)
   ) ENGINE=INNODB auto_increment=1114 default charset=utf8 COMMENT='用户表'
   ```

3. 一键生成说明

4. 改POM

5. 写YML

6. 主启动

7. 业务类

8. mvn package 命令将微服务形成新的jar包并上传到Linux服务器/mydocker目录下

9. 编写Dockerfile

   ```
   # 基础镜像使用java
   FEOM java:8
   # 作者
   MAINTAINER dzh
   # VOLUME 指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp
   VOLUME /tmp
   # 将jar包添加到容器中并更名为dzh_docker.jar
   ADD docker_boot-0.0.1-SNAPSHOT.jar dzh_docker.jar
   # 运行jar包
   RUN bash -c 'touch /dzh_docker.jar'
   ENTRYPOINT ["java", "-jar", "/dzh_docker.jar"]
   # 暴露6001端口作为微服务
   EXPOSE 6001
   ```

10. 构建镜像

    ```bash
    $sudo docker build -t dzh_docker:1.6 .
    ```

#### 2. 不用Compose

1. 单独的mysql容器实例

   1. 新建mysql容器实例

      ```bash
      $sudo docker run -p 3306:3306 --name mysql57 --privileged=true -v /zzyyuse/mysql/conf:/etc/mysql/conf.d -v /zzyyuse/mysql/logs:/logs -v /zzyyuse/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
      ```

   2. 进入mysql容器实例并新建库db2021+新建表t_user

2. 单独的redis容器实例

   ```bash
   $sudo docker run -p 6379:6379 --name redis608 --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data -d redis:6.0.8 redis-server /etc/redis/redis.conf
   ```

3. 微服务工程

   ```bash
   $sudo docker run -d -p 6001:6001 镜像ID
   ```

4. 上面三个容器实例一次顺序启动成功

   ![docker-23](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-23.png)

#### 3. swagger测试

http://localhost:你的微服务端口/swagger-ui.html#/

#### 4. 上面成功了，有哪些问题？

1. 先后顺序要求固定，先mysql+redis才能微服务访问成功
2. 多个run命令
3. 容器间的启停或者宕机，有可能导致IP地址对应的容器实例变化，映射出错，要么生产IP写死（可以但是不推荐），要么通过服务调用

#### 5. 使用Compose

1. 编写docker-compose.yml文件

   ```yml
   version: "3"
   
   services:
    microService:
     image: dzh_docker:1.6
     container_name: ms01
     ports:
      - "6001:6001"
     volumes:
      - /app/microService:/data
     networks:
      - atguigu_net
     depends_on:
      - redis
      - mysql
   
   redis:
    images: redis:6.0.8
    ports:
     - "6379:6379"
    volumes:
     - /app/redis/redis.conf:/etc/redis/redis.conf
     - /app/redis/data:/data
    networks:
     - atguigu_net
    command: redis-server /etc/redis/redis.conf
   
   mysql:
    image: mysql:5.7
    environment:
     MYSQL_ROOT_PASSWORD: '123456'
     MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
     MYSQL_DATABASE: 'db2021'
     MYSQL_USER: 'root'
     MYSQL_PASSWORD: '123456'
    ports:
     - "3306:3306"
    volumes:
     - /app/mysql/db:/var/lib/mysql
     - /app/mysql/conf/my.cnf:/etc/my.cnf
     - /app/mysql/init:/docker-entrypoint-initdb.d
    networks:
     - atguigu_net
    command: --default-authentication-plugin=mysql_native_password #解决外部无法访问
   
   networks:
    atguigu_net:
   
   ```

2. 第二次修改微服务工程docker_boot

   1. 写YML ，通过服务名访问，IP无关
   2. mvn package命令将微服务形成新的jar包并上传到Linux服务器/mydocker目录下
   3. 编写Dockerfile
   4. 构建镜像  docker build -t dzh_docker:1.6 .

3. 执行docker-compose up 或者执行 docker-compose up -d

4. 进入mysql容器实例并新建库db2021+新建表t_user

5. 测试通过

6. Compose常用命令

7. 关停

   ```bash
   $sudo docker-compose stop
   ```


## 十二、Docker轻量级可视化工具Portainer

### 1、是什么？

Portainer是一款轻量级的应用，它提供了图形化界面，用于方便管理Docker环境，包括单机环境和集群环境。

### 2、安装

1. 官网

   1. https://www.portainer.io/
   2. https://docs.portainer.io/start/install-ce/server/docker/linux

2. 步骤

   1. docker命令安装

      ```bash
      $sudo docker volume create portainer_data
      $sudo docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
      ```

   2. 第一次登录需创建admin，访问地址：IP地址:9000

   3. 设置admin用户和密码后首次登录

   4. 选择local选项卡后本地docker详细信息展示

### 3、登录并演示介绍常用case



## 十三、Docker容器监控之Cadvisor + InfluxDB + Granfana

### 1.原生命令

1. 操作

   1. 命令：==docker stats==

      ![docker-24](https://github.com/TangerineSea/KnowledgePond/raw/f3f0598f75bee7aa68005b011a433b50a87a98ce/picture/docker-24.png)

2. 问题

   docker stats命令可以很方便看到当前宿主机所有容器的cpu，内存以及网络流量等数据。但是，docker stats统计结果只能是当前宿主机的全部容器，数据资料是==实时的==，没有地方存储、没有健康指标过线预警等功能

### 2.是什么

**CAdvisor监控收集 + InfluxDB存储数据 + Granfana展示图表**

1. **CAdvisor**

   > 是一个容器资源监控工具，包括容器的内存，CPU，网络IO，磁盘IO等监控，同时提供了一个WEB页面用于查看容器的实时运行状态。CAdvisor默认存储2分钟的数据，而且只是针对单物理机。不过，CAdvisor提供了很多数据集成接口，支持InfluxDB，Redis，Kafka，Elasticsearch等集成，可以加上对应配置将监控数据发往这些数据库存储起来。

   > CAdvisor功能主要有两点：
   >
   > - 展示Host和容器两个层次的监控数据。
   > - 展示历史变化数据。

2. **InfluxDB**

   > InfluxDB是用Go语言编写的一个开源分布式时序、事件和指标数据库，无需外部依赖。
   >
   > CAdvisor默认只在本机保存最近2分钟的数据，为了持久化存储数据和统一手机展示监控数据，需要将数据存储到InfluxDB中。InfluxDB是一个时序数据库，准们用于存储时序相关数据，很适合存储CAdvisor的数据。而且，CAdvisor本身已经提供了InfluxDB的集成方法，丰启动容器时指定配置即可。
   >
   > InfluxDB主要功能：
   >
   > - 基于时间序列，支持与时间有关的相关函数（如最大、最小、求和等）。
   > - 可度量性：你可以实时对大量数据进行计算。
   > - 基于事件：它支持任意的事件数据。

3. **Granfana**

   > Granfana是一个开源的数据监控分析可视化平台，支持多找那个数据源配置（支持的数据源包括InfluxDB，MySQL，Elasticsearch，OpenTSDB，Graphite等）和丰富的插件及模板功能，支持图表权限控制和报警。
   >
   > Grafan主要特性：
   >
   > - 灵活丰富的图形化选项
   > - 可以混合多种风格
   > - 支持白天和夜间模式
   > - 多个数据源

### 3.compose容器编排

1. 新建目录
2. 新建3件套组合的docker-compose.yml
3. 启动docker-compose文件
4. 查看三个服务容器是否启动
5. 测试
   1. 浏览cAdvisor收集服务，http://ip:8080/
   2. 浏览influxdb存储服务，http://ip:8083/
   3. 浏览grafana展现服务，http://ip:3000/
      1. ip + 3000端口的方式访问，默认账户密码为admin
      2. 配置步骤：
         1. 配置数据源
         2. 选择influxdb数据源
         3. 配置细节
         4. 配置面板panel







































































































## **报错问题：**

1. 在执行docker run hello-world命令时，报错`docker: Error response from daemon: Get "https://registry-1.docker.io/v2/": dial tcp: lookup registry-1.docker.io on 192.168.2.2:53: server misbehaving.`，需要设置以下DNS服务器，执行以下命令：

   ````bash
   $ sudo vi /etc/resolv.conf
   #添加这两行
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ````

   
