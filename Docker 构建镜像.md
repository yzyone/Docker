# Docker 构建镜像（docker build） #

**docker build**

docker build命令用于从Dockerfile构建镜像。

**典型用法**

	docker build  -t ImageName:TagName dir

**选项**

- -t 给镜像加一个Tag
- ImageName 给镜像起的名称
- TagName 给镜像的Tag名
- Dir Dockerfile所在目录

**执行结果**

根据目录下的 Dockerfile 文件构建镜像

**例子**

	docker build -t chinaskill-redis:v1.1 .

- chinaskill-redis 是镜像名
- v1.1 是 tag 标签
- . 表示当前目录，即Dockerfile所在目录

**查看镜像**

现在我们使用docker images查看刚构建的镜像：

```
[root@master redis]# docker images

REPOSITORY             TAG      IMAGE ID         CREATED         SIZE
chinaskill-redis       v1.1     4f8e5f8ad012     2 hours ago     247MB
```

可以看到 chinaskill-redis 已经列在里面了。

**tips**

当使用Dockerfile构建镜像时，所在的目录一定要使用一个干净的目录（最好新建一个），以免目录下有其他文件（构建会加载当前目录下所有文件，导致磁盘爆满）。

————————————————

版权声明：本文为CSDN博主「小故事里的海」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/m0_61433200/article/details/125847217