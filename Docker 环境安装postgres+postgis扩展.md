# Docker 环境安装postgres+postgis扩展

### 1.单独安装Postgresql

1. 安装docker。（省略，自行百度安装）
2. 拉取postgresql镜像。

```text
docker pull postgres:12
```

3. 创建volumes

```text
docker volume create pg_data
```

4. 运行postgres

```text
docker run --name postgres --restart=always -e POSTGRES_PASSWORD=postgres -p 5432:25432 -v pg_data:/var/lib/postgresql/data -d postgres:12

// 进入postgres容器
docker exec -it postgres bash
// 登录数据库
psql -U postgres -W
```

### 2.安装postgis（包含了postgres）

1. 安装docker。（省略，自行百度安装）
2. 拉取postgis镜像。 （镜像里面已经包含了postgresql数据库）

```text
docker pull postgis/postgis:12-3.2
```

3. 创建volumes

```text
docker volume create postgis_data

docker volume create pg_data
```

4. 运行postgis

```text
docker run --name postgis  --restart=always -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DBNAME=gis_db -p 5432:5432 -v postgis_data:/var/lib/postgis/data -v pg_data:/var/lib/postgresql/data -d postgis/postgis:12-3.2
```



编辑于 2022-07-02 18:42