# [docker部署go的两种基础镜像](http://www.muzhuangnet.com/show/79421.html "docker部署go的两种基础镜像")



**一、 golang:latest 基础镜像**

```
mkdir gotest
touch main.go
touch Dockerfile
```

示例代码：  

```
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(writer http.ResponseWriter, request *http.Request) {
		fmt.Fprint(writer, "Hello World")
	})
	fmt.Println("3000!!")
	log.Fatal(http.ListenAndServe(":3000", nil))
}
```

Dockerfile配置

```
FROM golang:latest
WORKDIR $GOPATH/src/github.com/gotest
ADD . $GOPATH/src/github.com/gotest
RUN go build .
EXPOSE 3000
ENTRYPOINT ["./gotest"]
```

打包镜像

`docker build -t gotest .`


golang:latest 编译过程，其实就是在容器内，构建了一个go开发环境这种源镜像打包大概800M左右，比较大。

**二、 alpine:latest 基础镜像**  

- 使用此镜像大概过程就是，在linux机器，先把go程序打包成二进制文件，再丢到apine环境，执行编译好的文件。

- 默认情况下，Go的runtime环境变量CGO_ENABLED=1，即默认开始cgo，允许你在Go代码中调用C代码。通过设置CGO_ENABLED=0就禁用CGO了。所以需要执行：CGO_ENABLED=0 go build .即可。

- 此基础镜像打包只有13M，特别小。


```
#源镜像
FROM alpine:latest
#设置工作目录
WORKDIR $GOPATH/src/github.com/common
#将服务器的go工程代码加入到docker容器中
ADD . $GOPATH/src/github.com/common
#暴露端口
EXPOSE 3002
#最终运行docker的命令
ENTRYPOINT ["./common"]
```

打包镜像：

`docker build -t common .`

推荐教程：docker

以上就是[docker部署go的两种基础镜像](http://www.muzhuangnet.com/show/79421.html)的详细内容，更多文章请关注[木庄网络博客](http://www.muzhuangnet.com/)！
