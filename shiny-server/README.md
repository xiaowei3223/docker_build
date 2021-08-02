# shiny-server

这个镜像是专门用来运行ShinyApp的。基于ubuntu:18.04来创建的。

我们只需要将自己的ShinyApp放置在`/home/rstudio/shinyApps`目录下，就可以运行自己的shinyApp，浏览器打开的网址是：http://localhost:3838/your_shiny_name .



## Usage

使用下面的命令来运行这个shiny-server镜像。

```shell
sudo docker run -d \
    --publish=3838:3838 \
    --name shiny\
    moralab/shiny-server
```

- `-d` 表示在后台运行这个shiny-server镜像形成的容器。
- `--publish=3838:3838` 是将主机的`3838`端口和容器的`3838`端口形成映射关系，这样我们访问shiny-server页面时，就需要通过这个`3838`端口进行访问。如果我们想要更改到主机的其他端口号，只需要更改第一个`3838`。比如，使用主机的`8080`端口，这个参数的设置为`--publish=8080:3838`。后面的那个`3838`,我们不建议您在运行的时候，进行更改，因为它是容器默认的shiny-server的端口号。如果您需要更改第二个`3838`，您还需要更改shiny-server的配置文件。
- `--name shiny` 是将这个shiny-server镜像运行的容器命名为`shiny`,以便我们对其进行操作。
- `moralab/shiny-server`是这个镜像名称。

如果想要使用RStudio，可以使用`--publish=9004:8787`,运行下面的命令之后，访问http://localhost:8787。默认用户和密码是rstudio。

```shell
sudo docker run -d \
    --publish=3838:3838 \
    --publish=8787:8787 \
    --name shiny\
    moralab/shiny-server
```



如果想要在主机文件系统内保留自己的shinyApp，可以考虑以下的命令：

```shell
sudo docker run -d \
    --publish=3838:3838 \
    --publish=8787:8787 \
    --volume=`pwd`:/home/rstudio/shinyApps \
    --name shiny \
    moralab/shiny-server
```

`--volume`会将主机的当前目录挂载到容器内部`/home/rstudio/shinyApps`目录，该操作将会直接删除掉`/home/rstudio/shinyApps`目录下的所有文件。如果当前目录下存在文件，这些文件会被复制到容器内部`/home/rstudio/shinyApps`目录下。

在运行这个容器的过程中，任意在容器内部`/home/rstudio/shinyApps`或主机当前目录修改、移除、增加文件，两个目录下的文件内容会保持一致。

如果移除这个容器后，当前目录下的内容仍然会存在。

【注意】使用`--volume`时会首先删除掉`/home/rstudio/shinyApps`目录下的所有文件。



## 进入容器

```shell
# 进入容器内部，然后就可以像操作ubuntu那样进行操作。
docker exec -it shiny bash
```

## 创建这个镜像
```shell
git clone https://github.com/xiaowei3223/docker_build.git
cd docker_build/shiny-server
sudo docker build -t moralab/shiny-server:latest .
```

