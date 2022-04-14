# Docker Desktop 更改安装目录 #

docker desktop 默认是安装到“C:\Program Files\Docker”下的，无法更改，但是可以用创建联接的方式改变。
https://docs.docker.com/desktop/windows/install/

下载 docker Desktop

1，如果要按到D盘的\Program Files\下，先创建 “D:\Program Files\Docker”
然后

以管理员身份打开cmd并执行下列操作

	mklink /j "C:\Program Files\Docker" "D:\Program Files\Docker"

在执行安装程序

2，如果是已经安装完docker desktop ，先停止服务
然后剪切C:\Program Files\Docker 到“:\Program Files\Docker”
以管理员身份打开cmd并执行下列操作

	mklink /j "C:\Program Files\Docker" "D:\Program Files\Docker"


————————————————

版权声明：本文为CSDN博主「mengyu822_csdn」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/mengyu822_csdn/article/details/121990139