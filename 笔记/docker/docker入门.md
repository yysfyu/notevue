
# 一、docker安装（centos）

### 如果之前安装了旧版的docker，需要先删除：

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

### 安装仓库

```
sudo yum install -y yum-utils
​
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

![image-20221015143850602.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6989896c571341468e060f72c742d614~tplv-k3u1fbpfcp-watermark.image?)

### 安装docker引擎docker engine

```
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```


![image-20221015144132016.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84f652321dd347fa82572d89e4333171~tplv-k3u1fbpfcp-watermark.image?)
### 启动docker，运行hello world查看是否成功：

```
sudo systemctl start docker
sudo docker run hello-world
```

![image-20221015144359387.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c59f63b66ff84b018f33757dc9623a0a~tplv-k3u1fbpfcp-watermark.image?)

### 配置国内镜像仓库地址：

新建`/etc/docker/daemon.json`文件，输入如下内容：

```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://reg-mirror.qiniu.com",
    "https://docker.mirrors.ustc.edu.cn/"
  ]
}
```


![image-20221015144936929.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e24a7969f93e4b8f92267f2c8fa1b32a~tplv-k3u1fbpfcp-watermark.image?)
### 然后重启，配置开机启动：

```
sudo systemctl restart docker
sudo systemctl enable docker
sudo systemctl enable containerd
```


![image-20221015145047776.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0fa4753848d43e89aee2b040b14b0f8~tplv-k3u1fbpfcp-watermark.image?)
# 二、docker run开箱即用

## 1、docker架构图

![docker-architecture.svg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/71d3d31d05e34addaa09ee9e871bb936~tplv-k3u1fbpfcp-zoom-1.image)

### registry 镜像仓库

registry镜像仓库用于保存docker镜像image

Docker Hub是docker官方的镜像仓库，docker命令默认从docker hub中拉取镜像，我们也可以搭建自己的镜像仓库。

### image 镜像

image可以理解为一个只读的应用模板，相当于面向对象当中的类。image包含了应用程序及其所需要的依赖环境，例如可执行环境、环境变量、初始化脚本、启动命令等。

### container 容器

容器是image的一个运行实例，当我们运行一个image时，就创建了一个容器。

## 2、docker pull 拉取镜像

从镜像仓库拉取镜像到本地：例如拉取Nginx镜像

```
docker pull nginx   默认拉取的是最新的latest
docker pull nginx:latest
docker pull nginx:1.22
```

> 一般不建议使用latest，因为最新版是滚动更新的，过段时间，可能和本地的不是同一个。
>
> 使用docker images查看本地镜像


![image-20221015151220615.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/671705a637b44a15b518efd44af78451~tplv-k3u1fbpfcp-watermark.image?)
## 3、docker run 命令

`docker run [可选参数] 镜像名:版本 []`

### 公开端口（-p）

`docker run --name my-nginx1 -d -p 8080:80 nginx:1.22`


![image-20221015151502917.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98afc108fbfd416e9cd534ca18b7a953~tplv-k3u1fbpfcp-watermark.image?)
> 8080是宿主机端口，80是容器端口

> 使用`docker ps`查看正在运行的容器
>
> `docker start db-mysql` 启动容器
>
> `docker stop` `容器名或者id` 终止容器
>
> `docker rm 容器名或者id` 删除容器
>
> `docker ps -a`查看所有容器，包括已经停止的。


![image-20221015151543055.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f424a08e18a74b76af34aa1164713ebf~tplv-k3u1fbpfcp-watermark.image?)


![image-20221015152148315.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/25834e9ecaa04d66a5122d593dd68ad1~tplv-k3u1fbpfcp-watermark.image?)

默认情况下，容器无法通过外部网络访问。

需要使用`-p`参数将容器的端口映射到宿主机端口，才可以通过宿主机IP进行访问。

浏览器打开 http://宿主机ip:8080

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3577b68f27ab449f9782245c36accbd6~tplv-k3u1fbpfcp-zoom-1.image)

`-p 8080-8090:8080-8090`公开端口范围，前后必须对应

`-p 192.168.56.106:8080:80`如果宿主机有多个ip，可以指定绑定到哪个ip

### 后台运行

```
docker run --name db-mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

使用run命令，部署mysql，docker先去本地查找镜像，如果找不到，就去docker hub中拉取镜像

-   `--name` 定义容器的名称
-   `-e` 声明环境变量
-   `-d`容器在后台运行

<!---->

-   查看容器信息

```
docker inspect 容器名称
```

### 前台交互运行

创建一个新的容器，使用mysql客户端

```
docker run -it --rm mysql:5.7 mysql -h172.17.0.2 -uroot -p
```

`-it` 使用交互模式，可以在控制台里输入、输出

`--rm`**在容器退出时自动删除容器。** 一般在使用客户端程序时使用此参数。

如果每次使用客户端都创建一个新的容器，这样将占用大量的系统空间。

`mysql -h172.17.0.2 -uroot -p`表示启动容器时执行的命令。

-   `docker exec`在运行的容器中执行命令，一般配合`-it`参数使用交互模式

```
docker exec -it db-mysql /bin/bash
```

### 常用命令

-   `docker ps` 查看正在运行的容器
-   `docker ps -a` 查看所有容器，包括正在运行和停止的
-   `docker inspect` 查看容器的信息
-   `docker logs`查看日志
-   `docker cp` 在容器和宿主机间复制文件


# 三、docker网络

## 默认网络

docker会自动创建三个网络，`bridge、host、none`


![image-20221016133928962.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6182ebca91a7466ca6fa209f5549b45d~tplv-k3u1fbpfcp-watermark.image?)
> 命令`docker network ls`可查看

-   bridge桥接网络

    如果不指定，新建的容器默认将连接到bridge网络。

    默认情况下，使用bridge网络，宿主机可以ping通容器ip，容器中也能ping通宿主机。

    容器之间只能通过IP地址相互访问，由于容器的ip会随着启动顺序发生变化，所以不推荐使用ip访问。

-   host

    > 慎用，可能会有安全问题

    容器与宿主机共享网络，不需要映射端口即可通过宿主机ip访问（-p选项会被忽略）。

    主机模式网络可用于优化性能，在容器需要处理大量端口的情况下，它不需要网络地址转换(NAT)，并且不会为每个端口创建“用户空间代理”。

-   none

-   禁用容器中国所有网络，在启动容器时使用。

## 用户自定义网络

创建用户自定义网络

```
docker network create my-net
```


![image-20221016140146509.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/74db1d80129d4290931953dcde52bf72~tplv-k3u1fbpfcp-watermark.image?)
将已有容器连接到此网络

```
docker network connect my-net db-mysql
```


![image-20221016140122234.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1e9bdbe881f4d6baac57f5cf375fca4~tplv-k3u1fbpfcp-watermark.image?)

创建容器时指定网络。

```
docker run -it --rm --network my-net mysql:5.7 mysql -hdb-mysql -uroot -p
```

在用户自定义网络上，容器之间可以通过容器名进行访问。

用户自定义网络使用 Docker 的嵌入式 DNS 服务器将容器名解析成 IP。

## 主机名解析

#### hostname

容器的hostname默认为容器的 ID。

```
docker run -it -d --hostname my-alpine --name my-alpine  alpine:3.15
```

```
docker inspect \
    --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' my-alpine
```

#### /etc/hosts

在容器内手动修改/etc/hosts文件，容器重启后会恢复默认配置。

要使/etc/hosts修改生效，使用--add-host

```
docker run --add-host=my-alpine:172.17.0.3 -it --rm alpine:3.15
```

# 四、docker 存储

将数据存储在容器中，一旦容器被删除，数据也会被删除。同时也会使容器变得越来越大，不方便恢复和迁移。

将数据存储到容器之外，这样删除容器也不会丢失数据。一旦容器故障，我们可以重新创建一个容器，将数据挂载到容器里，就可以快速的恢复。

## 存储方式

docker 提供了以下存储选项

![image-20221016142648847.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a3a208ab70b1479e82d516223f9544a3~tplv-k3u1fbpfcp-watermark.image?)
-   **volume 卷**

**卷** 存储在主机文件系统分配一块专有存储区域，*由 Docker*（在 Linux 上）管理，并且与主机的核心功能隔离。非 Docker 进程不能修改文件系统的这一部分。卷是在 Docker 中持久保存数据的最佳方式。

-   **bind mount 绑定挂载**

**绑定挂载**可以将主机文件系统上目录或文件*装载到容器中*，但是主机上的非 Docker 进程可以修改它们，同时在**容器**中也可以更改**主机**文件系统，包括创建、修改或删除文件或目录，使用不当，可能会带来安全隐患。

-   **tmpfs** **临时挂载**

**tmpfs挂载**仅存储在主机系统的内存中，从不写入主机系统的文件系统。当容器停止时，数据将被删除。

## 绑定挂载（bind mount）

绑定挂载适用以下场景：

-   将配置文件从主机共享到容器。
-   在 Docker 主机上的开发环境和容器之间共享源代码或编译目录。

<!---->

-   -   例如，可以将 Maven 的`target/`目录挂载到容器中，每次在主机上用 Maven打包项目时，容器内都可以使用新编译的程序包。
-   #### **-v**
-   绑定挂载将主机上的目录或者文件装载到容器中。绑定挂载会覆盖容器中的目录或文件。
-   如果宿主机目录不存在，docker会自动创建这个目录。但是docker只自动创建文件夹，不会创建文件。
-   例如，mysql的配置文件和数据存储目录使用主机的目录。可以将配置文件设置为只读（read-only）防止容器更改主机中的文件。
-   ```
    docker run -e MYSQL_ROOT_PASSWORD=123456 \
               -v /home/mysql/mysql.cnf:/etc/mysql/conf.d/mysql.cnf:ro  \
               -v /home/mysql/data:/var/lib/mysql  \
               -d mysql:5.7 
    ```
-   #### --tmpfs 临时挂载
-   临时挂载将数据保留在主机内存中，当容器停止时，文件将被删除。
-   ```
    docker run -d -it --tmpfs /tmp nginx:1.22-alpine
    ```
-   ## volume 卷
-   卷 是docker 容器存储数据的首选方式，卷有以下优势：
-   -   卷可以在多个正在运行的容器之间共享数据。仅当显式删除卷时，才会删除卷。
    -   当你想要将容器数据存储在外部网络存储上或云提供商上，而不是本地时。
    -   卷更容易备份或迁移，当您需要备份、还原数据或将数据从一个 Docker 主机迁移到另一个 Docker 主机时，卷是更好的选择。
-   ### 创建和挂载卷
-   ```
    docker volume create my-data
    ​
    docker run -e MYSQL_ROOT_PASSWORD=123456 \
               -v /home/mysql/conf.d/my.cnf:/etc/mysql/conf.d/my.cnf:ro  \
               -v my-data:/var/lib/mysql  \
               -d mysql:5.7 
    ​
    ```
-   创建nfs卷
-   ```
    docker volume create --driver local \
        --opt type=nfs \
        --opt o=addr=192.168.1.1,rw \
        --opt device=:/path/to/dir \
        vol-nfs
    ```

# 五、docker镜像制作

## dockerfile

dockerfile通常包含以下几个常用命令：

```
FROM Ubuntu:18.04
WORKDIR /app
COPY . .
RUN make .
CMD python app.py
EXPOSE 80
```

`FROM` 打包使用的基础镜像

`WORKDIR`相当于`cd`命令，进入工作目录

`COPY` 将宿主机的文件复制到容器内

`RUN`打包时执行的命令，相当于打包过程中在容器中执行shell脚本，通常用来安装应用程序所需要的依赖、设置权限、初始化配置文件等

`CMD`运行镜像时执行的命令

`EXPOSE`指定容器在运行时监听的网络端口，它并不会公开端口，仅起到声明的作用，公开端口需要容器运行时使用-p参数指定。

## 制作自己的镜像

编写dockerfile文件

```
FROM openjdk:8u342-jre
WORKDIR /app
COPY ./ruoyi-admin.jar .
CMD [ "java", "-jar", "ruoyi-admin.jar" ]
EXPOSE 8080
```
