# Docker

# 1:Docker介绍

## 1.1:部署的问题

应用部署的环境问题

大型项目组件较多，运行环境也较为复杂，部署时会碰到一些问题：

- 依赖关系复杂，容易出现兼容性问题

- 开发、测试、生产环境的操作系统有差异





例如一个项目中，部署时需要依赖于node.js、Redis、RabbitMQ、MySQL等

这些服务部署时所需要的函数库、依赖项各不相同，甚至会有冲突。给部署带来了极大的困难

## 1.2:解决依赖兼容问题

而Docker确巧妙的解决了这些问题，Docker是如何实现的呢？

Docker为了解决依赖的兼容问题的，采用了两个手段：

- 将应用的Libs（函数库）、Deps（依赖）、配置与应用一起打包

- 将每个应用放到一个隔离容器去运行，避免互相干扰

![image-20230421194916906](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421194916906.png)

这样打包好的应用包中，既包含应用本身，也保护应用所需要的Libs、Deps

无需再操作系统上安装这些，自然就不存在不同应用之间的兼容问题了

## 1.3:解决操作系统兼容问题

虽然解决了不同应用的兼容问题，但是开发、测试等环境的操作系统会存在差异

操作系统版本也会有差异，怎么解决这些问题呢？



先来看看操作系统的架构：

结构包括：

- 计算机硬件：例如CPU、内存、磁盘等

- 系统内核：所有Linux发行版的内核都是Linux，例如CentOS、Ubuntu、Fedora等

  内核可以与计算机硬件交互，对外提供内核指令，用于操作计算机硬件

- 系统应用：操作系统本身提供的应用、函数库。这些函数库是对内核指令的封装，使用更加方便



应用于计算机交互的流程如下：

1）应用调用操作系统应用（函数库），实现各种功能

2）系统函数库是对内核指令集的封装，会调用内核指令

3）内核指令操作计算机硬件



无论什么Linux的发行版本，内核都是一样的，只是各个厂商的系统应用针对内核指令的封装不一样

我们要执行程序，本质就是操作硬件，就是一个对计算机硬件资源调度使用的指令序列

我们最后的目的就是使用系统应用提供的函数区操作内核，达到操作硬件的效果

正因为各个厂商对内核指令的封装不一样，则原本在乌班图系统能运行的到了Centos就不行了

Ubuntu和CentOS都是基于Linux内核，无非是系统应用不同，提供的函数库有差异：



那么Docker如何解决不同系统环境的问题？

- Docker将用户程序与所需要调用的系统(比如Ubuntu)函数库一起打包
- Docker运行到不同操作系统时，直接基于打包的函数库，借助于操作系统的Linux内核来运行



也就是Docker直接将这个程序所需要的函数进行打包，这样就不存在找不到函数库的情况了

函数库封装了针对内核指令集的操作，会调用内核指令

这样就可以把程序迁移到任意Linux操作系统【只要是使用的Linux内核】

## 1.4:程序的执行流程

程序的本质就是一个对计算机硬件资源调度使用的指令序列

![image-20230421195946301](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421195946301.png)

最开始：程序直接调用计算机硬件【难度大，只有资深专家可能才写得出来】

改进一：引入了操作系统，应用程序调用操作系统，操作系统调用硬件【比之前简单一点了】

改进二：引入了类似Java，Python，应用程序调用他们的类库去调用操作系统，操作系统调用硬件



这个也是我们目前的操作系统的架构，就变得越来越简单，我们只需要学会操作类库就行了

而不是我们之前那样去编码操作计算机硬件或者编码去操作我们的操作系统，那样难度太大

## 1.5:虚拟机执行流程

![image-20230421200453127](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421200453127.png)

我们平时安装的 Vmvare，其实叫做虚拟机操作系统，专门管理虚拟机的

虚拟机会以为自己存在于一个真实的操作系统，其实是依赖于 Hypervisor 虚拟出来的操作系统

这个上面有自己的虚拟硬件系统、虚拟操作系统，就跟操作系统一样

只不过虚拟机里面的程序在执行的时候，会执行虚拟机里面的操作系统指令，然后操作虚拟机硬件

这个时候会转到我们的虚拟机操作系统Vmvare

虚拟机操作系统 Vmvare 再去操作我们真实的操作系统指令，再去操作真实的硬件

也就是执行流程实际上是从上到下，进行了多次的转换



## 1.6:容器的执行流程

![image-20230421200936076](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421200936076.png)

容器是真实运行在宿主机的操作系统上面的，是直接依附于真实的操作系统

我们安装的Docker就是一个容器引擎，专门管理容器的

程序执行的时候，则会调用容器引擎，容器引擎调用操作系统，操作系统调用内核，内核调用硬件

比起原生的程序执行，仅仅多了一个容器引擎调用操作系统，比起虚拟机还是快得多的

## 1.7:Docker和虚拟机的区别

虚拟机是在操作系统中模拟硬件设备，然后运行另一个操作系统

比如在 Windows 系统里面运行 Ubuntu 系统，这样就可以运行任意的Ubuntu应用了

另外：虚拟机依赖于 Hypervisor 技术

![image-20230421201537385](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421201537385.png)

Docker 容器运行在 Docker 引擎之上，所有 Docker 容器共享同一个 Docker 引擎

但它们的运行又是相互隔离、互不干扰的

由于 Docker 容器不需要进行虚拟硬件及操作系统而是共享的宿主机的硬件与操作系统

所以 Docker 容器对系统资源的占用很少，其仅包含运行时必须的一些资源

所有 Docker 容器对于系统资源的使用都是由 Docker 引擎统一进行管理，所以对系统资源的利用率很高

无论是应用执行速度、内存损耗或者文件存储速度， 都要比传统虚拟机技术更高效

# 2:Docker架构

## 2.1:Docker Daemon

Docker Daemon 即 Docker 守护进程，其监听着 Docker API 请求并管理 Docker 对象

例如镜像、容器、网络和卷，守护进程还可以与其他守护进程通信以管理 Docker 服务

## 2.2:镜像和容器

Docker中有几个重要的概念：

镜像：Docker将应用程序及其所需的依赖、函数库、环境、配置等文件打包在一起，称为镜像

容器：镜像中的应用程序运行后形成的进程就是容器，只是Docker会给容器进程做隔离，对外不可见



一切应用最终都是代码组成，都是硬盘中的一个个的字节形成的文件，只有运行时，才会加载到内存，形成进程



镜像：就是把一个应用在硬盘上的文件、及其运行环境、部分系统函数库文件一起打包形成的文件包

容器：就是将这些文件中编写的程序、函数加载到内存中允许，形成进程，只不过要隔离起来



一个镜像可以启动多次，形成多个容器进程

注意：镜像是只读的，假设镜像是可写的，则很多容器都来写，则会污染镜像

![image-20230421201955639](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421201955639.png)



例如你下载了一个QQ，如果我们将QQ在磁盘上的运行文件及其运行的操作系统依赖打包，形成QQ镜像

然后你可以启动多次，双开、甚至三开QQ，跟多个妹子聊天



## 2.3:DockerHub

开源应用程序非常多，打包这些应用往往是重复的劳动

为了避免这些重复劳动，人们就会将自己打包的应用镜像，例如Redis、MySQL镜像放到网络上

这样就可以进行共享使用了，就像GitHub的代码共享一样



DockerHub：DockerHub是一个官方的Docker镜像的托管平台，这样的平台称为Docker Registry

国内也有类似于DockerHub 的公开服务，比如 [网易云镜像服务](https://c.163yun.com/hub)、[阿里云镜像库](https://cr.console.aliyun.com/)等

我们一方面可以将自己的镜像共享到DockerHub，另一方面也可以从DockerHub拉取镜像



## 2.4:Docker架构

我们要使用Docker来操作镜像、容器，就必须要安装Docker

Docker是一个CS架构的程序，由两部分组成：

- 服务端(server)：Docker守护进程，负责处理Docker指令，管理镜像、容器等

- 客户端(client)：通过命令或RestAPI向Docker服务端发送指令。可以在本地或远程向服务端发送指令



如图：

![image-20230421202214005](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421202214005.png)

# 3:Docker安装

## 3.2:安装版本

Docker 分为 CE 和 EE 两大版本

CE 即社区版（免费，支持周期 7 个月）

EE 即企业版，强调安全，付费使用，支持周期 24 个月

## 3.1:操作系统

这里主要介绍 Docker CE 在 CentOS上的安装

Docker CE 支持 64 位版本 CentOS 7，并且要求内核版本不低于 3.10

CentOS 7 满足最低内核的要求，所以我们在CentOS 7安装Docker

## 3.3:卸载(可选)

如果之前安装过旧版本的Docker，可以使用下面命令卸载：

```sh
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```



## 3.4:安装docker

首先需要我们的虚拟机联网，安装yum工具

```
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```



更新本地镜像源：

```
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo

yum makecache fast
```



安装

```
yum install -y docker-ce
```



## 3.5:启动docker

Docker应用需要用到各种端口，逐一去修改防火墙设置，非常麻烦，因此建议直接关闭防火墙

```
# 关闭防火墙
systemctl stop firewalld

# 禁止开机启动防火墙
systemctl disable firewalld
```



通过命令启动docker：

```
# 启动docker服务
systemctl start docker  

# 停止docker服务
systemctl stop docker  

# 重启docker服务
systemctl restart docker  
```



然后输入命令，可以查看docker版本：

```
docker -v
```

## 3.6:配置镜像

Docker官方镜像仓库网速较差，我们需要设置国内镜像服务：

参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

# 4:Docker镜像

## 4.1:镜像名称

首先来看下镜像的名称组成：

镜像名称一般分两部分组成：[repository] : [tag]

在没有指定tag时，默认是latest，代表最新版本的镜像

例如   mysql : 5.7   mongo : 4.4

这里的mysql就是repository，5.7就是tag，合一起就是镜像名称，代表5.7版本的MySQL镜像



## 4.2:镜像命令

帮助命令：docker xx --help命令查看某个语句的用法

例如使用：docker save  --help 查看 docker save 的用法



常见的镜像操作命令如图：

![image-20210731155649535](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20210731155649535.png)

- docker push：推送镜像到云服务
- docker pull：从云服务拉取镜像
- docker build：根据自定义的 Dockerfile 构建镜像
- docker save：把镜像保存为压缩包
- docker load：把压缩包加载为镜像
- docker images：查看所有的镜像
- docker rmi：删除某个镜像



## 4.3:练习

需求一：去DockerHub搜索并拉取一个Redis镜像

需求二：把刚刚拉取的镜像打包为压缩包

需求三：把刚刚拉取的镜像删除

需求四：把刚刚我们打包的压缩包再次加载为镜像



```
# 需求一
docker pull redis

# 需求二
docker save -o redis.tar redis:latest

# 需求三
docker rmi redis:latest

# 需求四
docker load -i redis.tar
```



# 5:Docker容器

## 5.1:容器相关命令

容器操作的命令如图：

![image-20210731161950495](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20210731161950495.png)

容器保护三个状态：

- 运行：进程正常运行
- 暂停：进程暂停，CPU不再运行，但是不释放内存
- 停止：进程终止，回收进程占用的内存、CPU等资源



其中：

- docker run：创建并运行一个容器，处于运行状态
- docker pause：让一个运行的容器暂停
- docker unpause：让一个容器从暂停状态恢复运行
- docker stop：停止一个运行的容器
- docker start：让一个停止的容器再次运行
- docker rm：删除一个容器
- docker exec：进入容器内部执行命令
- docker logs：查看某个容器的运行日志
- docker ps：查看所有运行的容器和状态
- docker ps -a：查看所有的容器和状态

## 5.2:创建容器

创建并运行nginx容器的命令：

```sh
docker run --name nginx  -p 80:80 -d nginx:latest
```



命令解读：

- docker run ：创建并运行一个容器
- --name : 给容器起一个名字，比如叫做 nginx
- -p ：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
- -d：后台运行容器
- nginx：镜像的定位，例如nginx:latest



这里的 -p 参数，是将容器端口映射到宿主机端口

默认情况下，容器是隔离环境，我们直接访问宿主机的80端口，肯定访问不到容器中的nginx

现在，将容器的80与宿主机的80关联起来

当我们访问宿主机的80端口时，就会被映射到容器的80，这样就能访问到nginx了：

![image-20230421205813977](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421205813977.png)



## 5.3:进入容器

需求：进入Nginx容器，修改HTML文件内容，添加  "你好，欢迎使用Docker"

提示：进入容器要用到docker exec命令



步骤：

1：进入容器，进入我们刚刚创建的 nginx 容器的命令为：

```sh
docker exec -it nginx bash
```



命令解读：

- docker exec ：进入容器内部，执行一个命令

- -it : 给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互

- nginx ：要进入的容器的名称

- bash：进入容器后执行的命令，bash是一个linux终端交互命令



2：进入 nginx 的HTML所在目录 /usr/share/nginx/html

容器内部会模拟一个独立的Linux文件系统，看起来如同一个linux服务器一样：

![image-20230421211244534](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421211244534.png)

nginx的环境、配置、运行文件全部都在这个文件系统中，包括我们要修改的html文件

查看 DockerHub 网站中的 nginx 页面

可以知道nginx的html目录位置在 ：/usr/share/nginx/html

我们执行命令，进入该目录：

```sh
cd /usr/share/nginx/html
```

可以查看到下面存在两个文件

分别是：50x.html  和  index.html



3：修改index.html的内容

容器内没有vi命令，无法直接修改，可以在内部下载 vim 编辑器之后进行修改



在浏览器访问自己的虚拟机地址，例如我的是：http://192.168.101.66，即可看到修改的结果



# 6:Docker数据卷

## 6.1:为什么需要数据卷

在之前的nginx案例中，修改nginx的html页面时，需要进入nginx内部

并且因为没有编辑器，修改文件也很麻烦

这就是因为容器与数据（容器内文件）耦合带来的后果



不便于修改：当我们修改容器的内容时需要进入容器，并且编辑十分麻烦

数据不可复用：容器之间是互相隔离的，在容器之间不可复用

升级维护困难：如果要升级容器，则需要先删除容器，这样数据就丢失了



要解决这个问题，必须将数据与容器解耦，这就要用到数据卷了



## 6.2:什么是数据卷

数据卷（volume）是一个虚拟的目录，用于指向宿主机文件系统中的某个目录

数据卷就是维护容器内文件或者文件夹以及容器内一些文件或者文件夹之间的关系的

是一个虚拟的东西，不是真正的实体文件或者文件夹



数据卷的根目录在：/var/lib/docker/volumes

当我们创建一个数据卷 html 的时候，则会在 /var/lib/docker/volumes 下面生成一个 html 目录

当我们创建一个数据卷 conf 的时候，则会在 /var/lib/docker/volumes 下面生成一个 conf 目录

![image-20230421221214993](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421221214993.png)

这样我们的数据卷就创建好了，但是还没有和容器进行绑定

假设我们把容器的 /usr/share/nginx/html 和 数据卷 html 绑定完成之后

这个时候对容器的一切操作都会作用在数据卷对应的宿主机目录了

这样，我们操作宿主机的 /var/lib/docker/volumes/html 目录

就等于操作容器内的 /usr/share/nginx/html 目录了



## 6.3:数据集操作命令

数据卷操作的基本语法如下：

```sh
docker volume [command]
```



docker volume命令是数据卷操作，可以查看帮助：docker volume --help

根据命令后跟随的command来确定下一步的操作：

- create 创建一个volume
- inspect 显示一个或多个volume的信息
- ls 列出所有的volume
- prune 删除未被容器使用的volume
- rm 删除一个或多个指定的volume

## 6.4:创建和查看数据卷

需求：创建一个数据卷，并查看数据卷在宿主机的目录位置

① 创建数据卷

```sh
docker volume create html
```

② 查看所有的数据卷

```sh
docker volume ls
```

③ 查看数据卷详细信息卷

```sh
docker volume inspect html
```

结果：

![image-20230421221652408](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421221652408.png)

可以看到，我们创建的html这个数据卷关联的宿主机目录为  /var/lib/docker/volumes/html/_data 目录

注意：数据卷的根目录在：/var/lib/docker/volumes

当我们创建一个数据卷 html 的时候，则会在 /var/lib/docker/volumes 下面生成一个 html 目录

但是并不能保证 html 目录下面没有更多的目录，比如上面默认的就含有一个 _data 目录



关联的这个挂载点才是宿主机真正的数据存放位置



## 6.5:挂载数据卷

我们以 Nginx 进行测试

我们在创建容器时，可以通过 -v 参数来挂载一个数据卷到某个容器内目录，命令格式如下：

docker run --name nginx -v html:/usr/share/nginx/html -p 80:80 -d nginx:latest



这里的-v就是挂载数据卷的命令：

-v html:/usr/share/nginx/html  就是把 html 数据卷挂载到容器内的 /usr/share/nginx/html 这个目录中



查看数据卷的具体挂载位置

docker volume inspect html



进入数据卷的具体挂载位置

cd /var/lib/docker/volumes/html/_data



修改文件，访问测试

vim index.html

## 6.6:挂载目录或文件

容器不仅仅可以挂载数据卷，也可以直接挂载到宿主机目录或者文件上，关联关系如下：

- 带数据卷模式：宿主机目录 --> 数据卷 ---> 容器内目录
- 直接挂载模式：宿主机目录 ---> 容器内目录

![image-20230421223031682](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421223031682.png)

语法：

目录挂载与数据卷挂载的语法是类似的：

- -v [宿主机目录]:[容器内目录]
- -v [宿主机文件]:[容器内文件]



需求：创建并运行一个MySQL容器，将宿主机目录直接挂载到容器



1：拉取一个镜像

docker pull mysql:8.0.26



2：在宿主机新建挂载目录

mkdir -p /home/docker/mysql/{data,conf}



3:创建配置文件

touch my.cnf

配置文件内容可以去网上搜索：直接复制即可



4:创建容器

```
docker run -p 3306:3306  \
 --name mysql \
 -v /home/docker/mysql/conf/my.cnf:/etc/my.cnf \
 -v /home/docker/mysql/data:/var/lib/mysql \
 -e MYSQL_ROOT_PASSWORD=123456 \
 -d mysql:8.0.26
```



## 6.7:数据卷小结

数据卷的作用：将容器与数据分离，解耦合，方便操作容器内数据，保证数据安全

数据卷操作：

- docker volume create：创建数据卷
- docker volume ls：查看所有数据卷
- docker volume inspect：查看数据卷详细信息，包括关联的宿主机目录位置
- docker volume rm：删除指定数据卷
- docker volume prune：删除所有未使用的数据卷



docker run的命令中通过 -v 参数挂载文件或目录到容器中：

- -v volume名称:容器内目录
- -v 宿主机文件:容器内文件
- -v 宿主机目录:容器内目录

数据卷挂载与目录直接挂载的

- 数据卷挂载耦合度低，由 Docker 来管理目录，但是目录较深，不好找
- 目录挂载耦合度高，需要我们自己管理目录，不过目录容易寻找查看

# 7:自定义镜像

常见的镜像在DockerHub就能找到，但是我们自己写的项目就必须自己构建镜像了

而要自定义镜像，就必须先了解镜像的结构才行

## 7.1:镜像结构

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包而成

简单来说，镜像就是在系统函数库、运行环境基础上，添加应用程序文件、配置文件、依赖文件等组合

然后编写好启动脚本打包在一起形成的一个文件

![image-20230421223927547](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230421223927547.png)

Docker镜像是一个分层的结构，我们要构建镜像，其实就是实现上述打包的过程

## 7.2:Dockerfile语法

构建自定义的镜像时，并不需要一个个文件去拷贝，打包

我们只需要告诉Docker，我们的镜像的组成，需要哪些BaseImage、需要拷贝什么文件

需要安装什么依赖、启动脚本是什么，将来Docker会帮助我们构建镜像



而描述上述信息的文件就是Dockerfile文件

Dockerfile 是用于构建 Docker 镜像的脚本文件，由一系列指令构成

通过 docker build 命令构建镜像时，Dockerfile 中的指令会由上到下依次执行，每条指令都将会构建出一个镜像

这就是镜像的分层。因此，指令越多，层次就越多，创建的镜像就越多，效率就越低

所以在定义 Dockerfile 时，能在一个指令完成的动作就不要分为两条



Dockerfile 就是一个文本文件，其中包含一个个的指令，用指令来说明要执行什么操作来构建镜像



注意：每一个指令都会形成一层Layer



常见的指令说明：

FROM：用于指定基础镜像，且必须是第一条指令；若省略了 tag，则默认为 latest

ENV：用于指定环境变量，这些环境变量，后续可以被 RUN 指令使用，key = value

COPY：拷贝本地的文件到镜像的指定目录

RUN：就是 shell 命令，docker build 执行过程中，会使用 shell 运行指定的命令

EXPOSE：指定容器准备对外暴露的端口号，但该端口号并不会真正的对外暴露

ENTRYPOINT：在执行 docker run 时，会调用执行 "EXECUTABLE" 指定的应用程序



## 7.3:构建Java项目一

需求：基于Ubuntu镜像构建一个新镜像，运行一个 Java 项目



步骤1：新建一个空文件夹 docker-demo

步骤2：拷贝一个Java程序 hello-1.0.0.jar 文件到 docker-demo 这个目录

步骤3：拷贝 jdk8.tar.gz 安装包文件到 docker-demo 这个目录

步骤4：编写 Dockerfile，放在 docker-demo 这个目录

```dockerfile
# 指定基础的镜像
FROM ubuntu:16.04

# 配置环境变量，JDK的安装目录，后续会使用
# 注意：/usr/local是容器内的目录
ENV JAVA_DIR=/usr/local

# 拷贝JDK和Java项目的包
# 把当前宿主机下的jdk8.tar.gz拷贝到容器内的/usr/local目录下
# 把当前宿主机下的docker-demo.jar拷贝到容器内的/tmp目录下
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./hello-1.0.0.jar /tmp/app.jar

# 安装JDK
# 进入/usr/local下，解压，重命名为java8
RUN cd $JAVA_DIR \
&& tar -xf ./jdk8.tar.gz \
&& mv ./jdk1.8.0_144 ./java8

# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin

# 暴露端口
EXPOSE 8888

# Java项目的启动命令，也是容器运行所执行的入口
ENTRYPOINT java -jar /tmp/app.jar
```

步骤5：进入 docker-demo

步骤6：运行命令：docker build -t javaweb:1.0 .



解释：

-t 参数，其实就是 tag，让我们指定生成的镜像的名称和版本

最后的那个 .  这个是指定 Dockerfile 的位置，我们现在全部在一个目录则使用 . 即可

最后运行容器：

docker run --name web -p 8888:8888 -d javaweb:1.0



测试访问：

![image-20230422104122552](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422104122552.png)

## 7.4:构建Java项目二

虽然我们可以基于Ubuntu基础镜像，添加任意自己需要的安装包，构建镜像，但是却比较麻烦

所以大多数情况下，我们都可以在一些安装了部分软件的基础镜像上做改造

例如，构建java项目的镜像，可以在已经准备了JDK的基础镜像基础上构建

事实上已经有了类似的JDK镜像了，我们之前的很多操作都可以省略



需求：基于 java:8-alpine 镜像，将一个Java项目构建为镜像



实现思路如下：

1：新建一个空的目录，然后在目录中新建一个文件，命名为 Dockerfile

2：拷贝一个Java程序 docker-demo.jar 文件到这个目录

3：编写 Dockerfile 文件：

- 基于java:8-alpine作为基础镜像
- 将app.jar拷贝到镜像中
- 暴露端口
- 编写入口ENTRYPOINT



Dockerfile 文件内容如下：

```dockerfile
# 指定基础镜像
FROM java:8-alpine

# 拷贝当前目录下的docker-demo.jar到容器的/tmp目录下
COPY ./hello-1.0.0.jar /tmp/app.jar

# 暴露端口
EXPOSE 8090

# Java项目的启动命令，也是容器运行所执行的入口
ENTRYPOINT java -jar /tmp/app.jar
```

4：使用docker build命令构建镜像：docker build -t javaweb:1.0 .

5：使用docker run创建容器并运行：docker run --name web -p 8888:8888 -d javaweb:1.0

![image-20230422104122552](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422104122552.png)

## 7.5:注意事项

注意：我们打包的时候有一个配置文件 application.yml，里面配置了端口

注意我们在编写 Dockerfile 的时候切记暴露端口的 EXPOSE 后面要写成一样的

不然是访问不了的，我们可以验证一下：

假设我们现在的 application.yml 配置的端口是 8100

但是我们在 Dockerfile 暴露端口写的 8888

这个时候我们构建镜像，运行容器 docker run --name web -p 8888:8888 -d javaweb:1.0

然后我们访问 http://192.168.101.66:8888/docker/hello

发现怎么都转不出来，但是我们的容器也是正常运行的，而且我们宿主机端口和暴露端口也是一致的

就很疑惑，这个时候我们查看一下 Docker 容器的日志

![image-20230422110756122](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422110756122.png)

发现内部还是启用的 8100 端口，所有问题解决了，也就是构建镜像的时候暴露的端口要和 application 的一致



## 7.6:小结

1：Dockerfile的本质是一个文件，通过指令描述镜像的构建过程

2：Dockerfile的第一行必须是FROM，从一个基础镜像来构建

3：基础镜像可以是基本操作系统，如Ubuntu，也可以是其他人制作好的镜像，例如JDK镜像

4：自定义镜像的时候暴露端口的 EXPOSE 后面要和Java程序的 application 里面写成一样的

# 8:DockerCompose

## 8.1:为什么需要它

Docker Compose可以基于Compose文件帮我们快速的部署分布式应用，而无需手动一个个创建和运行容器！

下面这个章鱼的手里就是拿着很多的容器，让它来为我们构建

![image-20230422111236692](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422111236692.png)

## 8.2:DockerCompose介绍

DockerCompose文件是一个文本文件，通过指令定义集群中的每个容器如何运行

我们已经知道了怎么把一个镜像运行成一个容器，DockerCompose其实就是用来运行多个容器的

其实可以理解为DockerCompose 就是 docker run 的集合



格式如下，是 yml 的文件格式：

```json
version: "3.8"
 services:
  mysql:
    image: mysql:8.0.26
    environment:
     MYSQL_ROOT_PASSWORD: JXLZZX79
    volumes:
     - "/tmp/mysql/data:/var/lib/mysql"
     - "/tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf"

  web:
    build: .
    ports:
     - "8090:8090"
```

上面的Compose文件就描述一个项目，其中包含两个容器：

- mysql：一个基于 mysql:8.0.26 镜像构建的容器，并且挂载了两个目录
- web：一个基于 docker build 临时构建的镜像容器，映射端口时8090



既然 DockerCompose 就是 docker run 的命令合集，则我们来对比一下

![image-20230422112423269](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422112423269.png)

```
docker run -p 3306:3306  \
 --name mysql \
 -v /home/docker/mysql/conf/my.cnf:/etc/my.cnf \
 -v /home/docker/mysql/data:/var/lib/mysql \
 -e MYSQL_ROOT_PASSWORD=123456 \
 -d mysql:8.0.26
```

其实很类似，只是语法结构不同，因为我们的mysql仅仅在微服务内部使用，所有可以不暴露端口，也可以暴露

还有 DockerCompose 没有写 -d ，默认就是 -d ，所有可以不写 -d 



DockerCompose的详细语法参考官网：https://docs.docker.com/compose/compose-file/

其实DockerCompose文件可以看做是将多个docker run命令写到一个文件，只是语法稍有差异

## 8.3:安装DockerCompose

Linux下需要通过命令下载：

```sh
# 下载
curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 慢的话使用这个【推荐】
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```



修改文件权限：

```sh
# 修改权限
chmod +x /usr/local/bin/docker-compose
```



Base自动补全命令：

```sh
# 补全命令
curl -L https://raw.githubusercontent.com/docker/compose/1.29.1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

## 8.4:配置一个实例

我们就不自己编写这个文件了，一般也不是我们开发来编写这个脚本文件，我们来解读一下即可

但是自己打包单个 Java 程序的镜像和运行是必会技能



先来看看目录结构：根目录是 cloud-demo ，下面是这些内容

![image-20230422114819331](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422114819331.png)



gateway 文件夹下的结构是一个 app.jar 和 Dockerfile 文件

mysql 文件夹下的结构就是一个 data 目录和 conf 目录

order-service 文件夹下的结构是一个 data 目录和 conf 目录

user-service 文件夹下的结构是一个 data 目录和 conf 目录



docker-compose.yml 的内容：

```yml
version: "3.2"

services:
  nacos:
    image: nacos/nacos-server
    environment:
      MODE: standalone
    ports:
      - "8848:8848"
  mysql:
    image: mysql:8.0.26
    environment:
      MYSQL_ROOT_PASSWORD: JXLZZX79
    volumes:
      - "$PWD/mysql/data:/var/lib/mysql"
      - "$PWD/mysql/conf:/etc/mysql/conf.d/"
  userservice:
    build: ./user-service
  orderservice:
    build: ./order-service
  gateway:
    build: ./gateway
    ports:
      - "80:80"
```



能看出来这个里面存在五个服务：nacos，mysql，userservice，orderservice，gateway



nacos的容器配置：镜像 nacos/nacos-server，单机运行，端口 8848

mysql的容器配置：镜像 mysql:8.0.26，密码 JXLZZX79，挂载了两个数据卷，不暴露端口

userservice的容器配置：就是执行 user-service 目录下的 Dockerfile，执行构建镜像运行容器，不暴露端口

orderservice的容器配置：就是执行 order-service 目录下的 Dockerfile，执行构建镜像运行容器，不暴露端口

gateway的容器配置：就是执行 gateway 目录下的 Dockerfile，执行构建镜像运行容器，端口 80



## 8.5:修改微服务配置

因为微服务将来要部署为docker容器，而容器之间互联不是通过IP地址，而是通过容器名

这里就将order-service、user-service、gateway服务的mysql、nacos地址都修改为基于容器名的访问

如下所示：

```yaml
spring:
  datasource:
    url: jdbc:192.168.101.66://mysql:3306/cloud_order?useSSL=false
    username: root
    password: JXLZZX79
    driver-class-name: com.mysql.cj.jdbc.Driver
  application:
    name: orderservice  # 这个时候在nacos里注册的服务名是docker容器名称了
  cloud:
    nacos:
      server-addr: nacos:8848 # nacos服务地址
```



## 8.6:部署DockerCompose

最后，我们需要将文件整个cloud-demo文件夹上传到虚拟机中，使用 DockerCompose 部署

进入 cloud-demo 文件夹下，也就是这里

![image-20230422114819331](https://zzx-note.oss-cn-beijing.aliyuncs.com/docker/image-20230422114819331.png)



在这里使用 ： docker-compose up -d

即可部署这下面的五个微服务了，会直接启动这五个服务

## 8.7:小结

Docker Compose 可以基于Compose文件帮我们快速的部署分布式应用，而无需手动一个个创建和运行容器

我们Java程序员一般不会去编写脚本部署这么多的服务，但是我们需要能看懂别人写的 Docker Compose 文件

主要把握把Java程序打包为镜像的操作，这个很重要！