# 基础

镜像与容器

当我们利用Docker安装应用时，Docker会自动搜索并下载应用**镜像(image)**。镜像不仅包含应用本身，还包含应用运行所需要的环境、配置、系统函数库。Docker会在运行镜像时创建一个隔离环境，称为**容器(container)**。

**镜像仓库：**存储和管理镜像的平台，Docker官方维护的一个公共仓库：Docker Hub

## 常见命令

### 命令介绍

Dcoker最常见的命令就是操作就镜像、容器的命令

![image-20250117153209558](D:\学习资料\实验室培训\运维\image\image-20250117153209558.png)



| docker命令     | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| docker pull    | 从镜像仓库拉取到本地镜像                                     |
| docker push    | 推送镜像到DockerRegistry                                     |
| docker images  | 查看本地所有镜像                                             |
| docker rmi     | 删除本地镜像                                                 |
| docker run     | 创建并运行容器（不能重复创建）                               |
| docker stop    | 停止指定容器                                                 |
| docker start   | 启动指定容器                                                 |
| docker ps      | 查看docker的正在运行的容器  -a:展示所有容器                  |
| docker rm      | 删除指定容器                                                 |
| docker logs    | 查看docker运行日志  -f：持续查看日志                         |
| docker exec    | 进入到容器内部，进行修改容器内的内容  -it+名字+bash：添加一个可交互的终端。exit：退出 |
| docker bulid   | 创建自定义的镜像                                             |
| docker save    | 保存镜像到本地压缩文件                                       |
| docker load    | 加载本地压缩文件到镜像                                       |
| docker inspect | 查看容器详细信息                                             |

默认情况下，每次重启虚拟机我们都需要手动启动Docker和Docker中的容器。通过命令可以实现开机自启：

```
# Docker开机自启
systemctl enable docker

# Docker容器开机自启
docker update --restart=always [容器名/容器id]
```

### **演示案例**：

查看DCokerHub，拉取Nginx镜像，创建并运行Nginx容器

需求：

- 在DCokerHub中搜索Nginx镜像，查看镜像的名称
- 拉取Nginx镜像
- 查看本地镜像列表
- 创建并运行Nginx容器
- 查看容器
- 停止容器
- 再次启动容器
- 进入Nginx容器
- 删除容器

```
# 第1步，去DockerHub查看nginx镜像仓库及相关信息

# 第2步，拉取Nginx镜像
docker pull nginx

# 第3步，查看镜像
docker images
# 结果如下：
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   16 months ago   141MB
mysql        latest    3218b38490ce   17 months ago   516MB

# 第4步，创建并允许Nginx容器
docker run -d --name nginx -p 80:80 nginx

# 第5步，查看运行中容器
docker ps
# 也可以加格式化方式访问，格式会更加清爽
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第6步，访问网页，地址：http://虚拟机地址

# 第7步，停止容器
docker stop nginx

# 第8步，查看所有容器
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第9步，再次启动nginx容器
docker start nginx

# 第10步，再次查看容器
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第11步，查看容器详细信息
docker inspect nginx

# 第12步，进入容器,查看容器内目录
docker exec -it nginx bash
# 或者，可以进入MySQL
docker exec -it mysql mysql -uroot -p

# 第13步，删除容器
docker rm nginx
# 发现无法删除，因为容器运行中，强制删除容器
docker rm -f nginx
```

### 命令别名

给常用Docker命令起别名，方便我们访问：

```
# 修改/root/.bashrc文件
vi /root/.bashrc
内容如下：
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

然后，执行命令使别名生效

```
source /root/.bashrc
```



## 数据卷

### 基础

容器是隔离环境，容器内程序的文件、配置、运行时产生的容器都在容器内部，我们要读写容器内的文件非常不方便。大家思考几个问题：

- 如果要升级MySQL版本，需要销毁旧容器，那么数据岂不是跟着被销毁了？
- MySQL、Nginx容器运行后，如果我要修改其中的某些配置该怎么办？
- 我想要让Nginx代理我的静态资源怎么办？

因此，容器提供程序的运行环境，但是**程序运行产生的数据、程序运行依赖的配置都应该与容器解耦**。

**数据卷（volume）**是一个**虚拟目录**，是**容器内目录**与**宿主机目录**之间映射的桥梁。

以Nginx为例，我们知道Nginx中有 两个关键的目录：

- `html`：放置一些静态资源
- `conf`：放置配置文件

如果我们要让Nginx代理我们的静态资源，最好是放到`html`目录；如果我们要修改Nginx的配置，最好是找到`conf`下的`nginx.conf`文件。

但遗憾的是，容器运行的Nginx所有的文件都在容器内部。所以我们必须利用数据卷将两个目录与宿主机目录关联，方便我们操作。如图

![image-20250117165816320](D:\学习资料\实验室培训\运维\image\image-20250117165816320.png)

在上图中：

- 我们创建了两个数据卷：`conf`、`html`
- Nginx容器内部的`conf`目录和`html`目录分别与两个数据卷关联。
- 而数据卷conf和html分别指向了宿主机的`/var/lib/docker/volumes/conf/_data`目录和`/var/lib/docker/volumes/html/_data`目录

这样以来，容器内的`conf`和`html`目录就 与宿主机的`conf`和`html`目录关联起来，我们称为**挂载**。此时，我们操作宿主机的`/var/lib/docker/volumes/html/_data`就是在操作容器内的`/usr/share/nginx/html/_data`目录。只要我们将静态资源放入宿主机对应目录，就可以被Nginx代理了。

```
注意：
/var/lib/docker/volumes这个目录就是默认的存放所有容器数据卷的目录，其下再根据数据卷名称创建新目录，格式为/数据卷名/_data。

为什么不让容器目录直接指向宿主机目录呢？
- 因为直接指向宿主机目录就与宿主机强耦合了，如果切换了环境，宿主机目录就可能发生改变了。由于容器一旦创建，目录挂载就无法修改，这样容器就无法正常工作了。
- 但是容器指向数据卷，一个逻辑名称，而数据卷再指向宿主机目录，就不存在强耦合。如果宿主机目录发生改变，只要改变数据卷与宿主机目录之间的映射关系即可。

不过，我们通过由于数据卷目录比较深，不好寻找，通常我们也允许让容器直接与宿主机目录挂载而不使用数据卷。
```

### 数据卷命令

数据卷的相关命令

| **命令**              | **说明**             |
| :-------------------- | :------------------- |
| docker volume create  | 创建数据卷           |
| docker volume ls      | 查看所有数据卷       |
| docker volume rm      | 删除指定数据卷       |
| docker volume inspect | 查看某个数据卷的详情 |
| docker volume prune   | 清除数据卷           |

注意：

- 容器与数据卷的挂载要在**创建容器时**配置，对于创建好的容器，是不能设置数据卷的。在执行docker run命令时，使用**-v 数据卷:容器内目录**可以完成数据卷挂载。
- **创建容器的过程中，如果挂载了数据卷且数据卷不存在，数据卷会自动创建**。

#### 演示案例1

利用Nginx容器部署静态资源

需求：

- 创建Nginx容器，修改nginx容器内的html目录下的index.html文件内容
- 将静态资源部署到nginx的html目录

```
# 1.首先创建容器并指定数据卷，注意通过 -v 参数来指定数据卷
docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx

# 2.然后查看数据卷
docker volume ls
# 结果
DRIVER    VOLUME NAME
local     29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f
local     html

# 3.查看数据卷详情
docker volume inspect html
# 结果
[
    {
        "CreatedAt": "2024-05-17T19:57:08+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/html/_data",
        "Name": "html",
        "Options": null,
        "Scope": "local"
    }
]

# 4.查看/var/lib/docker/volumes/html/_data目录
ll /var/lib/docker/volumes/html/_data
# 可以看到与nginx的html目录内容一样，结果如下：
总用量 8
-rw-r--r--. 1 root root 497 12月 28 2021 50x.html
-rw-r--r--. 1 root root 615 12月 28 2021 index.html

# 5.进入该目录，并随意修改index.html内容
cd /var/lib/docker/volumes/html/_data
vi index.html

# 6.打开页面，查看效果

# 7.进入容器内部，查看/usr/share/nginx/html目录内的文件是否变化
docker exec -it nginx bash
```

#### 演示案例2

mysql容器的数据挂载

需求：

- 查看mysql容器，判断是否有数据卷挂载
- 基于宿主机目录实现MySQL数据目录、配置文件、初始化脚本的挂载（查阅官方镜像文档）

##### MySQL的匿名数据卷

```
# 1.查看MySQL容器详细信息
docker inspect mysql
# 关注其中.Config.Volumes部分和.Mounts部分
```

我们关注两部分内容，第一是`.Config.Volumes`部分：

```
{
  "Config": {
    // ... 略
    "Volumes": {
      "/var/lib/mysql": {}
    }
    // ... 略
  }
}
```

可以发现这个容器声明了一个本地目录，需要挂载数据卷，但是**数据卷未定义**。这就是匿名卷。

然后，我们再看结果中的`.Mounts`部分：

```
{
  "Mounts": [
    {
      "Type": "volume",
      "Name": "29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f",
      "Source": "/var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data",
      "Destination": "/var/lib/mysql",
      "Driver": "local",
    }
  ]
}
```

可以发现，其中有几个关键属性：

- Name：数据卷名称。由于定义容器未设置容器名，这里的就是匿名卷自动生成的名字，一串hash值。
- Source：宿主机目录
- Destination : 容器内的目录

上述配置是将容器内的`/var/lib/mysql`这个目录，与数据卷`29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f`挂载。于是在宿主机中就有了`/var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data`这个目录。这就是匿名数据卷对应的目录，其使用方式与普通数据卷没有差别。



接下来，可以查看该目录下的MySQL的data文件：

```
ls -l /var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data
```

注意：每一个不同的镜像，将来创建容器后内部有哪些目录可以挂载，可以参考DockerHub对应的页面

### 挂载本地目录或文件

数据卷的目录结构较深，如果我们去操作数据卷目录会不太方便。在很多情况下，我们会直接将容器目录与宿主机指定目录挂载。挂载语法与数据卷类似：

```
在执行docker run命令时：
# 挂载本地目录
-v 本地目录:容器内目录
# 挂载本地文件
-v 本地文件:容器内文件
```

**注意**：本地目录或文件必须以 `/` 或 `./`开头，如果直接以名字开头，会被识别为数据卷名而非本地目录名。

```
-v mysql:/var/lib/mysql # 会被识别为一个数据卷叫mysql，运行时会自动创建这个数据卷
-v ./mysql:/var/lib/mysql # 会被识别为当前目录下的mysql目录，运行时如果不存在会创建目录
```

##### 删除并重新创建mysql容器，并完成本地目录挂载：

- 挂载`/root/mysql/data`到容器内的`/var/lib/mysql`目录

- 挂载`/root/mysql/init`到容器内的`/docker-entrypoint-initdb.d`目录（初始化的SQL脚本目录）

- 挂载`/root/mysql/conf`到容器内的`/etc/mysql/conf.d`目录（这个是MySQL配置文件目录）

  ```
  # 1.删除原来的MySQL容器
  docker rm -f mysql
  
  # 2.进入root目录
  cd ~
  
  # 3.创建并运行新mysql容器，挂载本地目录
  docker run -d \
    --name mysql \
    -p 3306:3306 \
    -e TZ=Asia/Shanghai \
    -e MYSQL_ROOT_PASSWORD=123 \
    -v /root/mysql/data:/var/lib/mysql \
    -v /root/mysql/conf:/etc/mysql/conf.d \
    -v /root/mysql/init:/docker-entrypoint-initdb.d \
    mysql
  
  # 4.查看root目录，可以发现~/mysql/data目录已经自动创建好了
  ls -l mysql
  # 结果：
  总用量 4
  drwxr-xr-x. 2 root    root   20 5月  19 15:11 conf
  drwxr-xr-x. 7 polkitd root 4096 5月  19 15:11 data
  drwxr-xr-x. 2 root    root   23 5月  19 15:11 init
  
  # 查看data目录，会发现里面有大量数据库数据，说明数据库完成了初始化
  ls -l data
  
  # 5.查看MySQL容器内数据
  # 5.1.进入MySQL
  docker exec -it mysql mysql -uroot -p123
  # 5.2.查看编码表
  show variables like "%char%";
  # 5.3.结果，发现编码是utf8mb4没有问题
  +--------------------------+--------------------------------+
  | Variable_name            | Value                          |
  +--------------------------+--------------------------------+
  | character_set_client     | utf8mb4                        |
  | character_set_connection | utf8mb4                        |
  | character_set_database   | utf8mb4                        |
  | character_set_filesystem | binary                         |
  | character_set_results    | utf8mb4                        |
  | character_set_server     | utf8mb4                        |
  | character_set_system     | utf8mb3                        |
  | character_sets_dir       | /usr/share/mysql-8.0/charsets/ |
  +--------------------------+--------------------------------+
  
  # 6.查看数据
  # 6.1.查看数据库
  show databases;
  # 结果，hmall是黑马商城数据库
  +--------------------+
  | Database           |
  +--------------------+
  | hmall              |
  | information_schema |
  | mysql              |
  | performance_schema |
  | sys                |
  +--------------------+
  5 rows in set (0.00 sec)
  # 6.2.切换到hmall数据库
  use hmall;
  # 6.3.查看表
  show tables;
  # 结果：
  +-----------------+
  | Tables_in_hmall |
  +-----------------+
  | address         |
  | cart            |
  | item            |
  | order           |
  | order_detail    |
  | order_logistics |
  | pay_order       |
  | user            |
  +-----------------+
  # 6.4.查看address表数据
  +----+---------+----------+--------+----------+-------------+---------------+-----------+------------+-------+
  | id | user_id | province | city   | town     | mobile      | street        | contact   | is_default | notes |
  +----+---------+----------+--------+----------+-------------+---------------+-----------+------------+-------+
  | 59 |       1 | 北京     | 北京   | 朝阳区    | 13900112222 | 金燕龙办公楼   | 李佳诚    | 0          | NULL  |
  | 60 |       1 | 北京     | 北京   | 朝阳区    | 13700221122 | 修正大厦       | 李佳红    | 0          | NULL  |
  | 61 |       1 | 上海     | 上海   | 浦东新区  | 13301212233 | 航头镇航头路   | 李佳星    | 1          | NULL  |
  | 63 |       1 | 广东     | 佛山   | 永春      | 13301212233 | 永春武馆       | 李晓龙    | 0          | NULL  |
  +----+---------+----------+--------+----------+-------------+---------------+-----------+------------+-------+
  4 rows in set (0.00 sec)
  ```

  



## 自定义镜像

### 镜像结构

镜像就是包含了应用程序、程序设计的系统函数库、运行配置等文件的文件包。构建镜像的过程其实就是把上述文件打包的过程。

- ```
  部署一个Java应用的步骤：
  
  - 1.准备一个Linux服务器
  - 2.安装JRE并配置环境变量
  - 3.拷贝jar包
  - 4.运行jar包
  ```

- ```
  构建一个Java镜像的步骤：
  - 1.准备一个Linux运行环境
  - 2.安装JRE并配置环境变量
  - 3.拷贝jar包
  - 4.编写运行脚本
  ```

上述步骤中的每一次操作其实都是在生产一些文件（系统运行环境、函数库、配置最终都是磁盘文件），所以**镜像就是一堆文件的集合**。

但需要注意的是，镜像文件不是随意堆放的，而是按照操作的步骤分层叠加而成，每一层形成的文件都会单独打包并标记一个唯一id，称为**Layer**（**层**）。这样，如果我们构建时用到的某些层其他人已经制作过，就可以直接拷贝使用这些层，而不用重复制作。

例如，第一步中需要的Linux运行环境，通用性就很强，所以Docker官方就制作了这样的只包含Linux运行环境的镜像。我们在制作java镜像时，就无需重复制作，直接使用Docker官方提供的CentOS或Ubuntu镜像作为基础镜像。然后再搭建其它层即可，这样逐层搭建，最终整个Java项目的镜像结构如图所示：![image-20250118204440518](D:\学习资料\实验室培训\运维\image\image-20250118204440518.png)

```
镜像结构
入口（Entrypoint）
镜像运行入口，一般是程序启动的脚本和参数
层（Layer）
在Baselmage基础上添加安装包、依赖、配置等，每次操作都形成新的一层
基础镜像（Baselmage）
应用依赖的系统函数库、环境、配置、文件等
```

分层好处：

```
- 可以共享一些基础镜像
- 可以分层优化
```

### Dockerfile

**Dockerfile**就是一个文本文件，其中包含一个个的**指令**（Instruction），用指令来说明要执行什么操作来构建镜像。将来Docker可以根据Dockerfile帮我们构建镜像。常见指令如下：

| **指令**       | **说明**                                     | **示例**                     |
| :------------- | :------------------------------------------- | :--------------------------- |
| **FROM**       | 指定基础镜像                                 | `FROM centos:6`              |
| **ENV**        | 设置环境变量，可在后面指令使用               | `ENV key value`              |
| **COPY**       | 拷贝本地文件到镜像的指定目录                 | `COPY ./xx.jar /tmp/app.jar` |
| **RUN**        | 执行Linux的shell命令，一般是安装过程的命令   | `RUN yum install gcc`        |
| **EXPOSE**     | 指定容器运行时监听的端口，是给镜像使用者看的 | EXPOSE 8080                  |
| **ENTRYPOINT** | 镜像中应用的启动命令，容器运行时调用         | ENTRYPOINT java -jar xx.jar  |

对应语法的官方文档：

https://docs.docker.com/engine/reference/builder/

作用：Dockerfile就是利用固定的指令来描述镜像的结构和构建过程，这样Docker才可以依次来构建镜像

**例如，要基于Ubuntu镜像来构建一个Java应用，其Dockerfile内容如下：**

```
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录、容器内时区
ENV JAVA_DIR=/usr/local
ENV TZ=Asia/Shanghai
# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar
# 设定时区
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8
# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
# 指定项目监听的端口
EXPOSE 8080
# 入口，java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

我们会有很多很多java项目需要打包为镜像，他们都需要Linux系统环境、JDK环境这两层，只有上面的3层不同（因为jar包不同）。如果每次制作java镜像都重复制作前两层镜像，会很麻烦。

所以，就有人提供了基础的系统加JDK环境，我们在此基础上制作java镜像，就可以省去JDK的配置了：

```
# 基础镜像
FROM openjdk:11.0-jre-buster
# 设定时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 拷贝jar包
COPY docker-demo.jar /app.jar
# 入口
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

### 构建镜像

当Dockerfile文件写好以后，就可以利用命令来构建镜像了。

```
# 进入镜像目录
cd /root/demo
# 开始构建
docker build -t docker-demo:1.0 .
```

命令说明：

- `docker build `: 就是构建一个docker镜像

- `-t docker-demo:1.0` ：`-t`参数是指定镜像的名称（`repository`和`tag`）

- `.` : (前面有个空格)最后的点是指构建时Dockerfile所在路径，由于我们进入了demo目录，所以指定的是`.`代表当前目录，也可以直接指定Dockerfile目录：

  ```
  # 直接指定Dockerfile目录
  docker build -t docker-demo:1.0 /root/demo
  ```

结果:

<img src="D:\学习资料\实验室培训\运维\image\image-20250118212643856.png" alt="image-20250118212643856" style="zoom:50%;" />

查看镜像列表：

```
# 查看镜像列表：
docker images
# 结果
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
docker-demo   1.0       d6ab0b9e64b9   27 minutes ago   327MB
nginx         latest    605c77e624dd   16 months ago    141MB
mysql         latest    3218b38490ce   17 months ago    516MB
```

然后尝试运行该镜像：

```
# 1.创建并运行容器
docker run -d --name dd -p 8080:8080 docker-demo:1.0
# 2.查看容器
dps
# 结果
CONTAINER ID   IMAGE             PORTS                                                  STATUS         NAMES
78a000447b49   docker-demo:1.0   0.0.0.0:8080->8080/tcp, :::8090->8090/tcp              Up 2 seconds   dd
f63cfead8502   mysql             0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   Up 2 hours     mysql

# 3.访问
curl localhost:8080/hello/count
# 结果：
<h5>欢迎访问黑马商城, 这是您第1次访问<h5>
```



## 网络

Java项目往往需要访问其它各种中间件，例如MySQL、Redis等。，我们的容器之间能否互相访问呢？

首先，我们查看下MySQL容器的详细信息，重点关注其中的网络IP地址：

```
# 1.用基本命令，寻找Networks.bridge.IPAddress属性
docker inspect mysql
# 也可以使用format过滤结果
docker inspect --format='{{range .NetworkSettings.Networks}}{{println .IPAddress}}{{end}}' mysql
# 得到IP地址如下：
172.17.0.2

# 2.然后通过命令进入dd容器
docker exec -it dd bash

# 3.在容器内，通过ping命令测试网络
ping 172.17.0.2
# 结果
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.053 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.059 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.058 ms
```

发现可以互联，没有问题

但是，容器的网络IP其实是一个虚拟的IP，其值并不固定与某一个容器绑定，如果我们在开发时写死某个IP，而在部署时很可能MySQL容器的IP会发生变化，连接会失败。

所以，我们必须借助于docker的网络功能来解决这个问题，官方文档：

https://docs.docker.com/engine/reference/commandline/network/

加入自定义网络的容器才可以通过容器名互相访问，Docker的网络操作命令如下：

| **命令**                  | **说明**                 | **文档地址**                                                 |
| :------------------------ | :----------------------- | :----------------------------------------------------------- |
| docker network create     | 创建一个网络             | [docker network create](https://docs.docker.com/engine/reference/commandline/network_create/) |
| docker network ls         | 查看所有网络             | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_ls/) |
| docker network rm         | 删除指定网络             | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_rm/) |
| docker network prune      | 清除未使用的网络         | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_prune/) |
| docker network connect    | 使指定容器连接加入某网络 | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_connect/) |
| docker network disconnect | 使指定容器连接离开某网络 | [docker network disconnect](https://docs.docker.com/engine/reference/commandline/network_disconnect/) |
| docker network inspect    | 查看网络详细信息         | [docker network inspect](https://docs.docker.com/engine/reference/commandline/network_inspect/) |

```
ip addr
```

- 可以看到虚拟机上的所有网卡

##### 演示案例：自定义网络

```
# 1.首先通过命令创建一个网络
docker network create hmall

# 2.然后查看网络
docker network ls
# 结果：
NETWORK ID     NAME      DRIVER    SCOPE
639bc44d0a87   bridge    bridge    local
403f16ec62a2   hmall     bridge    local
0dc0f72a0fbb   host      host      local
cd8d3e8df47b   none      null      local
# 其中，除了hmall以外，其它都是默认的网络

# 3.让dd和mysql都加入该网络，注意，在加入网络时可以通过--alias给容器起别名
# 这样该网络内的其它容器可以用别名互相访问！
# 3.1.mysql容器，指定别名为db，另外每一个容器都有一个别名是容器名
docker network connect hmall mysql --alias db
# 3.2.db容器，也就是我们的java项目
docker network connect hmall dd

# 4.进入dd容器，尝试利用别名访问db
# 4.1.进入容器
docker exec -it dd bash
# 4.2.用db别名访问
ping db
# 结果
PING db (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.056 ms
# 4.3.用容器名访问
ping mysql
# 结果：
PING mysql (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.054 ms
```

**总结**：

- 在自定义网络中，可以给容器起多个别名，默认的别名是容器名本身
- 在同一个自定义网络中的容器，可以通过别名互相访问 

# 部署

## MySQL部署

```
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql
```

- docker run  ：创建并运行一个容器
- **-d** 是让容器在后台运行

- **--name** ：给容器起个名字，**必须唯一**

- **-p 3306:3306**  ：设置端口映射

  - **容器是隔离环境**，外界不可访问。但是可以**将宿主机端口映射容器内到端口**，当访问宿主机指定端口时，就是在访问容器内的端口了。
  - 容器内端口往往是由容器内的进程决定，例如MySQL进程默认端口是3306，因此容器内端口一定是3306；而宿主机端口则可以任意指定，一般与容器内保持一致。
  - 格式： `-p 宿主机端口:容器内端口`，示例中就是将宿主机的3306映射到容器内的3306端口

- **-e KEY=VALUE** ：设置环境变量   （配置容器内进程运行时的一些参数）

  - 格式：`-e KEY=VALUE`，KEY和VALUE都由容器内进程决定
  - 案例中，`TZ``=Asia/Shanghai`是设置时区；`MYSQL_ROOT_PASSWORD=123`是设置MySQL默认密码

- **mysql**  ：指定运行的镜像的名字

  - **镜像命令规范**

    ```
    镜像名称一般分为两部分组成：[repository]:[tag]
    repository是镜像名称
    tag是镜像的版本
    如：mysql:5.7
    在没有指定tag时，默认时Latest,代表最新版本的镜像
    ```

    镜像的名称不是随意的，而是要到DockerRegistry中寻找，镜像运行时的配置也不是随意的，要参考镜像的帮助文档，这些在DockerHub网站或者软件的官方网站中都能找到。

## 部署Java应用

部署的容器及端口说明：

| **项目**     | **容器名** | **端口**           | **备注**            |
| :----------- | :--------- | :----------------- | :------------------ |
| hmall        | hmall      | 8080               | 黑马商城后端API入口 |
| hmall-portal | nginx      | 18080              | 黑马商城用户端入口  |
| hmall-admin  | 18081      | 黑马商城管理端入口 |                     |
| mysql        | mysql      | 3306               | 数据库              |

`hmall`项目是一个maven聚合项目，使用IDEA打开`hmall`项目，查看项目结构如图：

![image-20250118224843305](D:\学习资料\实验室培训\运维\image\image-20250118224843305.png)

我们要部署的就是其中的`hm-service`，其中的配置文件采用了多环境的方式：![image-20250118224827073](D:\学习资料\实验室培训\运维\image\image-20250118224827073.png)

其中的`application-dev.yaml`是部署到开发环境的配置，`application-local.yaml`是本地运行时的配置。

查看application.yaml，你会发现其中的JDBC地址并未写死，而是读取变量：![image-20250118224911797](D:\学习资料\实验室培训\运维\image\image-20250118224911797.png)



这两个变量在`application-dev.yaml`和`application-local.yaml`中并不相同：

![image-20250118224936404](D:\学习资料\实验室培训\运维\image\image-20250118224936404.png)

在dev开发环境（也就是Docker部署时）采用了mysql作为地址，刚好是我们的mysql容器名，只要两者在一个网络，就一定能互相访问。

我们将项目打包：![image-20250118225005973](D:\学习资料\实验室培训\运维\image\image-20250118225005973.png)

结果：![image-20250118225023960](D:\学习资料\实验室培训\运维\image\image-20250118225023960.png)



将`hm-service`目录下的`Dockerfile`和`hm-service/target`目录下的`hm-service.jar`一起上传到虚拟机的`root`目录：![image-20250118225045748](D:\学习资料\实验室培训\运维\image\image-20250118225045748.png)

部署项目：

```
# 1.构建项目镜像，不指定tag，则默认为latest
docker build -t hmall .

# 2.查看镜像
docker images
# 结果
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
hmall         latest    0bb07b2c34b9   43 seconds ago   362MB
docker-demo   1.0       49743484da68   24 hours ago     327MB
nginx         latest    605c77e624dd   16 months ago    141MB
mysql         latest    3218b38490ce   17 months ago    516MB

# 3.创建并运行容器，并通过--network将其加入hmall网络，这样才能通过容器名访问mysql
docker run -d --name hmall --network hmall -p 8080:8080 hmall
```

测试，通过浏览器访问：http://虚拟机地址:8080/search/list

## 部署前端

`hmall-portal`和`hmall-admin`是前端代码，需要基于nginx部署。nginx的部署目录：![image-20250118225200601](D:\学习资料\实验室培训\运维\image\image-20250118225200601.png)

其中：

- `html`是静态资源目录，我们需要把`hmall-portal`以及`hmall-admin`都复制进去
- `nginx.conf`是nginx的配置文件，主要是完成对`html`下的两个静态资源目录做代理

我们现在要做的就是把整个nginx目录上传到虚拟机的`/root`目录下：![image-20250118225217639](D:\学习资料\实验室培训\运维\image\image-20250118225217639.png)

然后创建nginx容器并完成两个挂载：

- 把`/root/nginx/nginx.conf`挂载到`/etc/nginx/ng``inx.conf`
- 把`/root/nginx/html`挂载到`/usr/share/nginx/html`

由于需要让nginx同时代理hmall-portal和hmall-admin两套前端资源，因此我们需要暴露两个端口：

- 18080：对应hmall-portal
- 18081：对应hmall-admin

命令如下：

```
docker run -d \
  --name nginx \
  -p 18080:18080 \
  -p 18081:18081 \
  -v /root/nginx/html:/usr/share/nginx/html \
  -v /root/nginx/nginx.conf:/etc/nginx/nginx.conf \
  --network hmall \
  nginx
```

测试，通过浏览器访问：http://虚拟机ip:18080

## DockerCompose

我们部署一个简单的java项目，其中包含3个容器：

- MySQL
- Nginx
- Java项目

而稍微复杂的项目，其中还会有各种各样的其它中间件，需要部署的东西远不止3个。如果还像之前那样手动的逐一部署，就太麻烦了。

而Docker Compose就可以帮助我们实现**多个相互关联的Docker容器的快速部署**。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器。

### 基本语法

docker-compose.yml文件的基本语法可以参考官方文档：

https://docs.docker.com/compose/compose-file/compose-file-v3/

docker-compose文件中可以定义多个相互关联的应用容器，每一个应用容器被称为一个服务（service）。由于service就是在定义某个应用的运行时参数，因此与`docker run`参数非常相似。

举例来说，用docker run部署MySQL的命令如下：

```
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  --network hmall
  mysql
```

如果用`docker-compose.yml`文件来定义，就是这样

```
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
    networks:
      - new
networks:
  new:
    name: hmall
```

对比如下：

| **docker run 参数** | **docker compose 指令** | **说明**   |
| :------------------ | :---------------------- | :--------- |
| --name              | container_name          | 容器名称   |
| -p                  | ports                   | 端口映射   |
| -e                  | environment             | 环境变量   |
| -v                  | volumes                 | 数据卷配置 |
| --network           | networks                | 网络       |

黑马商城部署文件：

```
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on:
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall
```

### 基础命令

编写好docker-compose.yml文件，就可以部署项目了。常见的命令：

https://docs.docker.com/compose/reference/

基本语法如下：

```Bash
docker compose [OPTIONS] [COMMAND]
```

其中，OPTIONS和COMMAND都是可选参数，比较常见的有：

| **类型** | **参数或指令** | **说明**                                                     |
| -------- | -------------- | ------------------------------------------------------------ |
| Options  | -f             | 指定compose文件的路径和名称                                  |
| Options  | -p             | 指定project名称。project就是当前compose文件中设置的多个service的集合，是逻辑概念 |
| Commands | up             | 创建并启动所有service容器                                    |
| Commands | down           | 停止并移除所有容器、网络                                     |
| Commands | ps             | 列出所有启动的容器                                           |
| Commands | logs           | 查看指定容器的日志                                           |
| Commands | stop           | 停止容器                                                     |
| Commands | start          | 启动容器                                                     |
| Commands | restart        | 重启容器                                                     |
| Commands | top            | 查看运行的进程                                               |
| Commands | exec           | 在指定的运行中容器中执行命令                                 |

演示：

```
# 1.进入root目录
cd /root

# 2.删除旧容器
docker rm -f $(docker ps -qa)

# 3.删除hmall镜像
docker rmi hmall

# 4.清空MySQL数据
rm -rf mysql/data

# 5.启动所有, -d 参数是后台启动
docker compose up -d
# 结果：
[+] Building 15.5s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                    0.0s
 => => transferring dockerfile: 358B                                                    0.0s
 => [internal] load .dockerignore                                                       0.0s
 => => transferring context: 2B                                                         0.0s
 => [internal] load metadata for docker.io/library/openjdk:11.0-jre-buster             15.4s
 => [1/3] FROM docker.io/library/openjdk:11.0-jre-buster@sha256:3546a17e6fb4ff4fa681c3  0.0s
 => [internal] load build context                                                       0.0s
 => => transferring context: 98B                                                        0.0s
 => CACHED [2/3] RUN ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo   0.0s
 => CACHED [3/3] COPY hm-service.jar /app.jar                                           0.0s
 => exporting to image                                                                  0.0s
 => => exporting layers                                                                 0.0s
 => => writing image sha256:32eebee16acde22550232f2eb80c69d2ce813ed099640e4cfed2193f71  0.0s
 => => naming to docker.io/library/root-hmall                                           0.0s
[+] Running 4/4
 ✔ Network hmall    Created                                                             0.2s
 ✔ Container mysql  Started                                                             0.5s
 ✔ Container hmall  Started                                                             0.9s
 ✔ Container nginx  Started                                                             1.5s

# 6.查看镜像
docker compose images
# 结果
CONTAINER           REPOSITORY          TAG                 IMAGE ID            SIZE
hmall               root-hmall          latest              32eebee16acd        362MB
mysql               mysql               latest              3218b38490ce        516MB
nginx               nginx               latest              605c77e624dd        141MB

# 7.查看容器
docker compose ps
# 结果
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
hmall               root-hmall          "java -jar /app.jar"     hmall               54 seconds ago      Up 52 seconds       0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
mysql               mysql               "docker-entrypoint.s…"   mysql               54 seconds ago      Up 53 seconds       0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp
nginx               nginx               "/docker-entrypoint.…"   nginx               54 seconds ago      Up 52 seconds       80/tcp, 0.0.0.0:18080-18081->18080-18081/tcp, :::18080-18081->18080-18081/tcp
```

打开浏览器，访问：http://yourIp:8080









**参考文档**

[‬‌‌‬⁠‬‌‬‍﻿⁠﻿‌⁠﻿﻿‌‍⁠‬⁠⁠‌‌‌‍⁠⁠day02-Docker - 飞书云文档](https://b11et3un53m.feishu.cn/wiki/MWQIw4Zvhil0I5ktPHwcoqZdnec)