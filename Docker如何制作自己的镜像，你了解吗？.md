# Docker如何制作自己的镜像，你了解吗？

## ***一、准备工作***

准备一个用来制作镜像的容器。这里我们使用centos安装apache的容器。容器的制作方法如下：

```text
# 1.使用centos启动一个交互式容器docker run -it centos:latest /bin/bash
# 2.安装apacheyum -y install httpd
# 3.退出容器exit
```

操作步骤如下：

```bash
[root@localhost ~]# docker run -it centos:latest /bin/bash
[root@a554ba6ed056 /]# yum -y install httpd
Failed to set locale, defaulting to C.UTF-8
...
  mod_http2-1.11.3-3.module_el8.2.0+307+4d18d695.x86_64                

Complete!
[root@a554ba6ed056 /]# exit
```

```text
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
a554ba6ed056        centos:latest       "/bin/bash"         About a minute ago   Exited (0) 33 seconds ago                       musing_wilson
```

这里能看到我们的容器ID为a554ba6ed056，就是用该容器，制作一个自己的镜像。

## ***二、使用docker commit制作镜像***

语法如下：

```text
docker commit <container的ID> <新的image_name>
```

示例如下：

```text
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
a554ba6ed056        centos:latest       "/bin/bash"         About a minute ago   Exited (0) 33 seconds ago                       musing_wilson
[root@localhost ~]# docker commit a554ba6ed056 centos:apache
sha256:9cb1f6b6242fd29032772b9507505ed6fc953fbc31adf90e550af93b07823eed
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              apache              9cb1f6b6242f        9 seconds ago       255 MB
docker.io/centos    latest              831691599b88        6 weeks ago         215 MB
[root@localhost ~]# docker run -it centos:apache /bin/bash
[root@61d98f417e87 /]# rpm -qa httpd
httpd-2.4.37-21.module_el8.2.0+382+15b0afa8.x86_64
```

当前的镜像ID为a554ba6ed056，新的镜像的名称为centos:apache。镜像创建成功后，进入镜像内部执行rpm -qa httpd，能正确的查询该软件包。

## ***三、使用docker build制作镜像***

使用docker build创建镜像时，需要使用Dockerfile文件自动化制作镜像。Dockerfile的执行过程，很像源码编译时./configure后产生的Makefile。

### **1.创建工作目录**

```text
# 1.创建并进入目录mkdir /docker-build && cd /docker-build
# 2.创建自动化构建文件touch Dockerfile
```

make 自动化编译时需要Makefile文件，自动化创建镜像时，也需要Dockerfile。

```text
[root@localhost ~]# mkdir /docker-build && cd /docker-build
[root@localhost docker-build]# touch Dockerfile
[root@localhost docker-build]# ls -a
.  ..  Dockerfile
```

### **2.编辑Dockerfile文件**

在Dockerfile文件中编辑自己的自定义镜像的操作，可以包含用户指定软件的依赖等。使用vim将下列内容写入到Dockerfile文件中。

```text
FROM centos:latest
MAINTAINER <hx@hxsen.com>
RUN yum -y install httpd
ADD start.sh /usr/local/bin/start.sh
ADD template /var/www/html/
CMD /usr/local/bin/start.sh
```

注释：

```
| 关键词        | 说明                                                                                      |
| ---------- | --------------------------------------------------------------------------------------- |
| FROM       | 基于哪个镜像                                                                                  |
| MAINTAINER | MAINTAINER镜像创建者                                                                         |
| RUN        | yum -y install httpd安装软件用                                                               |
| ADD        | 将文件[src]拷贝到新产生的镜像的文件系统对应的路径[dest]。所有拷贝到新镜像中的文件和文件夹权限为 0755,uid 和 gid 为 0                |
| CMD        | docker实例启动成功后，会执行CMD后面的命令。所以CMD后面一般跟需要开机启动的服务或脚本。一个Dockerfile 中只能有一条CMD命令，多条则只执行最后一条CMD |
```

### **3.创建服务及文件**

创建start.sh脚本启动的httpd服务和apache默认首页index.html文件。

设置脚本：

```text
# 1. 设置启动脚本
echo "/usr/sbin/httpd -DFOREGROUND" > start.sh
# 2. 给启动脚本添加运行权限
chmod a+x start.sh。
```

默认首页文件：

```text
# 创建模板目录
mkdir template
```

之后，将自己的index.html文件放置到template文件夹里面，我的index.html内容是hello world。使用命令build来创建新的image语法如下：

```text
docker build -t [父镜像名]:[镜像的tag] [Dockerfile文件所在路径]

-t：表示tag，镜像名
```

实例：

```text
docker build -t centos:httpd ./
```

```text
[root@localhost docker-build]# docker build -t centos:httpd ./
Sending build context to Docker daemon 4.608 kB
Step 1/6 : FROM centos:latest
 ---> 831691599b88
Step 2/6 : MAINTAINER <hx@hxsen.com>
 ---> Running in 01ae32a4e2fd
 ---> acf758c5234d
Removing intermediate container 01ae32a4e2fd
Step 3/6 : RUN yum -y install httpd
 ---> Running in d3ecdcc793d5
...
Step 4/6 : ADD start.sh /usr/local/bin/start.sh
 ---> 05d06ac82917
Removing intermediate container 4e5992822142
Step 5/6 : ADD template /var/www/html/
 ---> b7b8b4efdc5b
Removing intermediate container 9cf72dd07cf8
Step 6/6 : CMD /usr/local/bin/start.sh
 ---> Running in 17973309cca9
 ---> 7e03de04b894
Removing intermediate container 17973309cca9
Successfully built 7e03de04b894
[root@localhost docker-build]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              httpd               7e03de04b894        4 minutes ago       255 MB
docker.io/centos    latest              831691599b88        6 weeks ago         215 MB
```

执行成功。内容挺多的，只保留了关键的步骤。列出所有的镜像后，能看到自己的TAG为httpd的镜像。

## ***四、本地镜像***

**1.保存镜像到本地**

保存镜像到tar包，语法：

```text
docker save -o [导出的镜像名.tar] [本地镜像名]：[镜像标签]
```

实例：

```text
docker save -o docker-centos-httpd-image.tar centos:httpd
```

```text
[root@localhost ~]# docker save -o docker-centos-httpd-image.tar centos:httpd
[root@localhost ~]# ls -a 
.   abc.txt          .bash_history  .bash_profile  .cshrc   docker-centos-httpd-image.tar  .tcshrc   .viminfo
..  anaconda-ks.cfg  .bash_logout   .bashrc        def.txt  .pki                           test.txt
```

保存成功，能看到本地的tar包docker-centos-httpd-image.tar。**2.使用本地镜像**

语法：

```text
docker load -i [本地tar包文件]
```

实例：

```text
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              httpd               7e03de04b894        20 minutes ago      255 MB
docker.io/centos    latest              831691599b88        6 weeks ago         215 MB
[root@localhost ~]# docker rmi 7e03de04b894
Untagged: centos:httpd
Deleted: sha256:7e03de04b894cff137074740df18ac967e681ce022636707890b2ad1e896364d
...
[root@localhost ~]# docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/centos    latest              831691599b88        6 weeks ago         215 MB
[root@localhost ~]# docker load -i docker-centos-httpd-image.tar
d25caed0fdbe: Loading layer [==================================================>] 39.81 MB/39.81 MB
805e763ef330: Loading layer [==================================================>] 3.584 kB/3.584 kB
b1f7a1e7f61a: Loading layer [==================================================>] 3.584 kB/3.584 kB
Loaded image: centos:httpd
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              httpd               7e03de04b894        22 minutes ago      255 MB
docker.io/centos    latest              831691599b88        6 weeks ago         215 MB
```

我这里使用的是同一个系统操作的。所以需要把之前的镜像删除，然后加载自己的本地镜像。加载后，可以成功看到显示了自己的加载后的镜像。

## ***五、发布镜像***

这里将镜像发布到[http://hub.docker.com](https://link.zhihu.com/?target=http%3A//hub.docker.com)，docker的官方仓库。

**1.需要到[https://hub.docker.com/](https://link.zhihu.com/?target=https%3A//hub.docker.com/)注册账号。**

**2.使用命令行登录到docker hub。**

```text
docker login
```

实例：

```text
[root@localhost ~]# docker login -u houxin -p 123456
Login Succeeded
```

**3.发布镜像**

```text
docker push houxin/centos:httpd
```

如果直接这样发布，命令行可能提示找不到本地镜像。我们需要对我们发布的镜像加标签，标签为houxin/centos:httpd。然后再发布就行了。

```text
docker tag centos:httpd houxin/centos:httpd
```

实例：

```text
[root@localhost ~]# docker tag centos:httpd houxin/centos:httpd
[root@localhost ~]# docker push houxin/centos:httpd
The push refers to a repository [docker.io/houxin/centos:httpd]
b1f7a1e7f61a: Pushed 
805e763ef330: Pushed
d25caed0fdbe: Pushed 
eb29745b8228: Mounted from library/centos 
latest: digest: sha256:6c17e3ace7397a56f023bbc322e404bd6a4aa44492e3f5dd9b8852f312e9348e size: 1155
```

```text
[root@localhost ~]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
houxin/centos                   httpd               7e03de04b894        About an hour ago   255 MB
centos-httpd                    latest              7e03de04b894        About an hour ago   255 MB
centos                          httpd               7e03de04b894        About an hour ago   255 MB
docker.io/centos                latest              831691599b88        6 weeks ago         215 MB
```

能看到自己的houxin/centos下的TAG为httpd的镜像。

### **4.下载镜像**

```text
docker pull houxin/centos:httpd
```

示例：

```text
[root@localhost ~]# docker pull houxin/centos:httpd
Trying to pull repository docker.io/houxin/centos ... 
httpd: Pulling from docker.io/houxin/centos
Digest: sha256:6c17e3ace7397a56f023bbc322e404bd6a4aa44492e3f5dd9b8852f312e9348e
Status: Downloaded newer image for docker.io/houxin/centos:httpd
```

![](https://pic3.zhimg.com/80/v2-d1269e75628054bc7371402e74d1ecee_720w.jpg)
