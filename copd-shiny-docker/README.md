# COPD-Shiny

这个实际上是为了https://github.com/mora-lab/TC-DATABASE_shiny而创建的一个镜像。

该镜像依赖于自建的shiny-server的镜像。

该镜像包含了neo4j、shiny-server、rstudio-server这三个软件。其中，neo4j是用来运行这个shinyApp所需要的`Time-Course-COPD-Database`，而shiny-server则包含了https://github.com/mora-lab/TC-DATABASE_shiny的shinyApp文件。为了方便能够使用到R，rstudio-server便也安装在这个镜像中了。



## Usage

使用以下的命令来运行这个镜像。

```shell
#运行该docker镜像
docker run -d \
     --publish=7474:7474 --publish=7687:7687 \
     --publish=3838:3838 \
     --publish=8787:8787 \
     --volume=`pwd`/neo4j:/home/neo4j/data \
     --volume=`pwd`/shinyApp:/home/shiny/shinyApp \
    --name copd-shiny \
    moralab/copd-shiny
```

- `-d` 表示在后台运行这个`COPD-shiny`镜像形成的容器。
- ` --publish=7474:7474 --publish=7687:7687` 是用来访问neo4j的。
- `--publish=3838:3838`是用来访问shinyApp的。
- `--publish=8787:8787`是用来访问RStudio-server的。
- `--volume=`pwd`/neo4j:/home/neo4j/data` 将当前目录下的neo4j文件夹与容器内的`/home/neo4j/data`绑定在一起，以方便更换neo4j数据库。
- `--volume=`pwd`/shinyApp:/home/shiny/shinyApp`将当前目录下的`shinyApp`与容器内的`/home/shiny/shinyApp`绑定在一起，以方便添加shinyApp。
- `--name copd-shiny` 是将这个镜像运行的容器命名为`copd-shiny`,以便我们对其进行操作。
- ` moralab/copd-shiny`是这个镜像名称。

运行后，可以通过http://localhost:3838/shinyApp/copd_shiny来访问这个COPD-Shiny的页面。

此外，您还可以通过http://localhost:7474访问`Time-Course-COPD-Database`数据库。登录的账户和密码是`neo4j`。

如果您想要使用RStudio,可以访问http://localhost:8787，登录的账户和密码是`rstudio`。



