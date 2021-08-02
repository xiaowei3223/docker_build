# Neo4j 

这个Neo4j镜像运行的是`neo4j-community-3.5.28`的版本，是基于`ubuntu:18.04`的镜像创建的。

主要原因是官方的Neo4j版本用的是Debian的系统，我本人不太熟悉这个系统，于是只好自己创建一个了。另外根据实验室的需求，也将graph-algorithms和apoc-procedures这两个常用的插件配置好了。也是因为实验室的需求，这个neo4j的选用的版本仍然是在`3-*`系列的,因为它比较适合R语言的爱好者来使用R操作Neo4j。



## Usage

使用下面的命令，运行这个neo4j镜像。

```shell
#运行该docker镜像
sudo docker run -d \
    --publish=7474:7474 --publish=7687:7687  \
    --volume=`pwd`/neo4j:/home/neo4j/data \
    --name neo4j \
    moralab/neo4j-3.5.4
```

- `-d` 表示在后台运行这个`neo4j-3.5.4`镜像形成的容器。
- ` --publish=7474:7474 --publish=7687:7687` 是将主机的`7474`、`7687`端口分布与容器的`7474`、`7687`端口形成映射关系，这样我们访问neo4j页面时，就需要通过这个`7474`端口进行访问。如果我们想要更改到主机的其他端口号，只需要更改第一个`7474`或`7687`。比如，使用主机的`8080`端口，这个参数的设置为`--publish=8080:8080`。后面的那个`7474`、`7687`,我们不建议您在运行的时候，进行更改，因为它是容器默认的`neo4j`的端口号。
- `--volume`参数是将主机的文件目录与容器指定的目录绑定在一块，当您修改当前目录的文件（使用`pwd`来指定当前目录）时，容器内的`/home/neo4j/data`也会发生改变。反过来，也是如此。
- `--name neo4j` 是将这个neo4j镜像运行的容器命名为`neo4j`,以便我们对其进行操作。
- ` moralab/neo4j-3.5.4`是这个镜像名称。

当您运行后，打开http://localhost:7474/就可以访问neo4j的浏览器页面，默认用户名和密码都是`neo4j`。



## 运行自己的数据库

### 方式1 
在您需要使用自己的数据库时，仅需要将您的数据库文件夹拷贝到当前目录下（即您运行上述命令的路径下）的`databases/graph.db`文件夹中。   
使用下面这个命令重启neo4j容器就可以运行。
```shell
sudo docker restart neo4j
```
【注意】不要在`databases/graph.db`再创建文件夹来存储你的数据库文件。而是删除graph.db文件夹里面的所有内容后，再拷贝你的数据库进去。   

### 方式2
如果你仍然想要使用自己的数据库名称，则将你的数据库完整地拷贝到`databases`目录下，然后使用以下的命令进入容器内
```shell  
sudo docker exec -it neo4j bash
```
然后往`/home/neo4j/conf/neo4j.conf`文件中添加你的数据库`mydata.db`，如使用以下命令添加
```shell
# 安装编辑软件vim
apt-get update && apt-get install -y vim
vim /home/neo4j/conf/neo4j.conf
```
然后在该文件中添加
```shell
dbms.active_database=mydata.db
```
保存后，退出编辑模式。
最后，使用`exit`退出容器后，重启neo4j容器即可。

## 创建这个镜像
```shell
git clone https://github.com/xiaowei3223/docker_build.git
cd docker_build/neo4j
sudo docker build -t moralab/shiny-server:neo4j-3.5.4:latest .
```
