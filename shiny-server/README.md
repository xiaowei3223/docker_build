# shiny-server

这个镜像是专门用来运行ShinyApp的。基于ubuntu:18.04来创建的。

我们只需要将自己的ShinyApp放置在`/home/shiny/shinyApp`目录下，就可以运行自己的shinyApp，浏览器打开的网址是：http://localhost:3838/shinyApp/your_shiny_name .

如果不能运行，考虑进入容器内部安装运行ShinyApp所需的R包，当所有R包都安装好后，就可以运行了。



## Usage

使用下面的命令来运行这个shiny-server镜像。

```shell
docker run -d \
    --publish=3838:3838 \
    --volume=`pwd`:/home/shiny/shinyApp \
    --name shiny\
    moralab/shiny-server
```

- `-d` 表示在后台运行这个shiny-server镜像形成的容器。
- `--publish=3838:3838` 是将主机的`3838`端口和容器的`3838`端口形成映射关系，这样我们访问shiny-server页面时，就需要通过这个`3838`端口进行访问。如果我们想要更改到主机的其他端口号，只需要更改第一个`3838`。比如，使用主机的`8080`端口，这个参数的设置为`--publish=8080:3838`。后面的那个`3838`,我们不建议您在运行的时候，进行更改，因为它是容器默认的shiny-server的端口号。如果您需要更改第二个`3838`，您还需要更改shiny-server的配置文件。
- `--volume`参数是将主机的文件目录与容器指定的目录绑定在一块，当您修改当前目录的文件（使用`pwd`来指定当前目录）时，容器内的`/home/shiny/shinyApp`也会发生改变。反过来，也是如此。
- `--name shiny` 是将这个shiny-server镜像运行的容器命名为`shiny`,以便我们对其进行操作。
- `moralab/shiny-server`是这个镜像名称。



## 进入容器

```shell
# 进入容器内部，然后就可以像操作ubuntu那样进行操作。
docker exec -it shiny bash
```



