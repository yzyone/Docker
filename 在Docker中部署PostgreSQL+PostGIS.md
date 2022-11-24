# 在Docker中部署PostgreSQL+PostGIS

> 如果你愿意通过Docker运行PostgreSQL，我们建议使用与PostGIS捆绑在一起的Kartoza的Docker recipe作为扩展。

![PostGIS](./postgis/20200918084130684.png)

在5432端口（或任何端口）上创建名为postgrest_tut的Docker Postgres容器。

```
sudo docker run --name "postgrest_tut" -p5432:5432 -e POSTGRES_MULTIPLE_EXTENSIONS=postgis -d -t kartoza/postgis
```

必须配置Postgres，以确保它信任连接（本例为参考，不应以这种方式在生产环境中使用）。

```
sudo docker exec -it postgrest_tut bash
```

首先在容器内安装一个编辑器，例如nano，然后导航到Postgres配置所在的文件夹。

```
apt update && apt install nano && apt install vim
#这也可能是不同的版本，这取决于你的安装
cd /etc/postgresql/12/main/
```

在文件pg_hba.conf中，需要对“数据库管理登录通过Unix域套接字（应该在第85行）下的设置从peer更改为trust，然后重新启动Docker容器。

```
sudo docker restart postgrest_tut
```

之后，执行以下命令，该命令将弹出psql提示符。

```
sudo docker exec -it postgrest_tut psql -U postgres
```

在提示中，必须使用以下命令启用PostGIS扩展：

```
postgres=# CREATE EXTENSION postgis;
postgres=# \q
```

安装PostGIS，以便在以后的阶段使用raster2pgsql：

```
sudo docker exec -it postgrest_tut bash -c "apt-get update && apt-get install postgis"
```

安装PostgREST  
为了简单起见，安装postgrest（参照postgrest.org网站这取决于你的操作系统）。  
安装好所有内容后，你就可以简单地运行PostgREST：

```
postgrest
```

如果一切正常，会打印出版本和配置信息。  
PostgREST-创建API模式  
Postgrest将需要它自己的API模式，因此再次打开Docker容器的psql提示符（如果它运行在主机操作系统上，也可以使用psql-uppostgres）。

```
sudo docker exec -it postgrest_tut psql -U postgres

psql (9.6.3)
Type "help" for help.

postgres=#
```

为将通过PostgREST API公开的数据库对象创建任意命名的模式。在psql提示符内执行以下SQL语句：

```
CREATE SCHEMA api;
```

接下来，你应该添加一个用于匿名web请求的角色。当请求到达API时，PostgREST将切换到这个数据库角色来运行查询。

```
CREATE ROLE web_anon NOLOGIN;

GRANT USAGE ON SCHEMA api TO web_anon;
```

现在，webanon角色有权访问api模式中的函数。  
正如PostgREST的作者所指出的，创建一个专用的角色来连接数据库，而不是使用高权限的postgres角色，这实际上是一个很好的实践。为此，请命名角色验证器，并授予此用户切换到web角色的能力：

```
CREATE ROLE authenticator NOINHERIT LOGIN PASSWORD 'gisops';
GRANT web_anon TO authenticator;
```

为了确保我们在空间计算中使用合适的投影，我们将使用World Robinson的投影EPSG:54030英寸需要案例。请将其添加到你的数据库中：

```
INSERT into spatial_ref_sys (srid, auth_name, auth_srid, proj4text, srtext) values ( 54030, 'ESRI', 54030, '+proj=robin +lon_0=0 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs ', 'PROJCS["World_Robinson",GEOGCS["GCS_WGS_1984",DATUM["WGS_1984",SPHEROID["WGS_1984",6378137,298.257223563]],PRIMEM["Greenwich",0],UNIT["Degree",0.017453292519943295]],PROJECTION["Robinson"],PARAMETER["False_Easting",0],PARAMETER["False_Northing",0],PARAMETER["Central_Meridian",0],UNIT["Meter",1],AUTHORITY["EPSG","54030"]]');
```

PostgREST需要配置文件来指定数据库连接。继续创建一个名为gisops的文件-教程.conf包含以下信息（如果在前面的步骤中更改了端口和密码，请记住要调整端口和密码）。

```
db-uri = "postgres://authenticator:gisops@localhost:5432/postgres"
db-schema = "api"
db-anon-role = "web_anon"
server-port = 3000
```

现在我们准备开始PostgREST。

```
postgrest gisops-tutorial.conf
# or ./postgrest gisops-tutorial.conf
```

你应该可以看到这样的东西：

```
Listening on port 3000
Attempting to connect to the database...
Connection successful
```

PostgREST服务器现在可以为web请求提供服务了。
