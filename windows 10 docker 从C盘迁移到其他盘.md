# windows 10 docker 从C盘迁移到其他盘

docker 默认安装到c 盘,c 盘又太小，蛋疼，只有把它迁移了。

**目录**

停止 docker 相关进程 

停止hyper-v 服务

修改 hyper-v 和docker 相关路径

将C:\Program Files\Docker 全部拷贝到H 盘

将 C:\ProgramData\DockerDesktop 全部拷贝到H盘

修改docker 服务路径

环境变量修改

最后进入H盘，双击 启动docker 成功了

---

### 停止 docker 相关进程

[![windows 10 docker 从C盘迁移到其他盘_第1张图片](./win10/ededaaab7fdd46eba47433f47fc79a9f.jpg)](https://img.it610.com/image/info8/ededaaab7fdd46eba47433f47fc79a9f.jpg)

### 停止hyper-v 服务

打开-->控制面板\系统和安全\管理工具\Hyper-V 管理器

[![windows 10 docker 从C盘迁移到其他盘_第2张图片](./win10/a69c1cd5e8d9481b8c86eacc5454d2f0.jpg)](https://img.it610.com/image/info8/a69c1cd5e8d9481b8c86eacc5454d2f0.jpg)

### 修改 hyper-v 和docker 相关路径

[![windows 10 docker 从C盘迁移到其他盘_第3张图片](./win10/f0792aa40c6a4dada8b693de30a82154.jpg)](https://img.it610.com/image/info8/f0792aa40c6a4dada8b693de30a82154.jpg)

[![windows 10 docker 从C盘迁移到其他盘_第4张图片](./win10/25682fbd7e5c436ead9eb2c802c46545.jpg)](https://img.it610.com/image/info8/25682fbd7e5c436ead9eb2c802c46545.jpg)

### 将C:\Program Files\Docker 全部拷贝到H 盘

[![windows 10 docker 从C盘迁移到其他盘_第5张图片](./win10/2bf436d2c7be4985ba9f75a0e48e49c3.jpg)](https://img.it610.com/image/info8/2bf436d2c7be4985ba9f75a0e48e49c3.jpg)

### 将 C:\ProgramData\DockerDesktop 全部拷贝到H盘

[![windows 10 docker 从C盘迁移到其他盘_第6张图片](./win10/0159cc4147c84e039dc6497ada34a93d.jpg)](https://img.it610.com/image/info8/0159cc4147c84e039dc6497ada34a93d.jpg)

### 修改docker 服务路径

此时启动会报docker 服务启动出错

运行“regedit”打开注册表，在“HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services”中查找“com.docker.service”，

将ImagePath 修改为 H盘的路径

[![windows 10 docker 从C盘迁移到其他盘_第7张图片](./win10/0d1cc172ec6e4f13a0d4f15840bdf38a.jpg)](https://img.it610.com/image/info8/0d1cc172ec6e4f13a0d4f15840bdf38a.jpg)

### 环境变量修改

[![windows 10 docker 从C盘迁移到其他盘_第8张图片](./win10/1aeb66fb7525475f9a79da40d60e2630.jpg)](https://img.it610.com/image/info8/1aeb66fb7525475f9a79da40d60e2630.jpg)

### 最后进入H盘，双击 启动docker 成功了

[![windows 10 docker 从C盘迁移到其他盘_第9张图片](./win10/442580c01ec8428c8727582abce045e7.jpg)](https://img.it610.com/image/info8/442580c01ec8428c8727582abce045e7.jpg)




