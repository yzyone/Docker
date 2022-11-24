# 修改Docker镜像存储目录，减轻系统盘负担 #

https://author.baidu.com/home?from=bjh_article&app_id=1597536250129171)

2022-04-21 11:45四川

关注

![img](./images/9f510fb30f2442a76f1d25f1dea39b41d01302b9.jpg)



Docker在安装完成之后，默认的镜像imags目录在/var/lib/docker下边，可以看到，这是根目录下的文件，一般占用的是系统盘的空间，而我们在使用过程中，Docker随着时间的增长，镜像会越来越多，会出现系统盘空间紧张的情况，这个时候，就需要迁移docker目录了。

迁移docker目录的方式有三种：

### 一、使用linux系统软链方式

------

```
ln -s /mnt/docker/ /var/lib/docker
```

小白PS：/var/lib/docker是原目录，/mnt/docker是你的数据盘目录，说简单点就是/mnt/docker目录就是/var/lib/docker目录了。

### 二、在启动项文件里改目录

这种方式比较简单，适用版本也比较多。如果你是apt方式安装的docker，那么路径一般在：

```
/etc/systemd/system/multi-user.target.wants/docker.service
```

用nano或vim工具打开这个文件，找到Execstart部分，在这行末尾添加--graph=新目录，如：

```
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --graph=/mnt/docker
```



### 三、官方配置文件daemon.json中修改 

这种方式比较建议，另外docker镜像加速也可以配置在这里，还是使用nano或vim打开/etc/docker/daemon.json文件，配置代码如下：

```
｛
“registry-mirrors”:["https://hub-mirror.c.163.com"],
"data-root":"/mnt/docker"
｝
```

小白PS：registry-mirrors是加速镜像用的配置，data-root是新镜像目录。

保存之后，重启docker服务，命令如下：

```
systemctl restart docker
```

所有配置完成之后，可以通过docker info命令查看设置的目录
