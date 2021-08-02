FROM moralab/shiny-server

#  作者信息
MAINTAINER Mora-Lab <moralabgalaxy@163.com>

# 为shiny app 安装必要的R包-----------------------------------------------------------------------

# 这些软件是因为安装R包时所需要的
# RUN apt install -y git
# RUN apt-get install -y libxml2-dev \
# 	gfortran \
#	libpcre2-dev \
#	libbz2-dev \
#	zlib1g-dev

# 安装需要的R包
RUN R -e "install.packages('BiocManager', repos='http://cran.rstudio.com/')" && \
	R -e "install.packages('visNetwork', repos='http://cran.rstudio.com/')" && \
	R -e "install.packages('ggalluvial', repos='http://cran.rstudio.com/')" && \
	R -e "install.packages('ggplot2', repos='http://cran.rstudio.com/')" && \
	R -e "options(repos='http://cran.rstudio.com/'); devtools::install_github('nicolewhite/RNeo4j')" && \
	R -e "install.packages('DT', repos='http://cran.rstudio.com/')"  && \
	R -e "install.packages('igraph', repos='http://cran.rstudio.com/')" && \
	R -e "install.packages('xlsx', repos='http://cran.rstudio.com/')" 

# 安装JAVA 因为安装neo4j 需要------------------------------------------------------------------------
RUN apt-get update && \
	apt-get install -y default-jre default-jdk 

# 安装neo4j --------------------------------------------------------------------------------------
WORKDIR /home/
RUN wget -O neo4j-community-3.5.28-unix.tar.gz \
	https://neo4j.com/artifact.php?name=neo4j-community-3.5.28-unix.tar.gz && \
	tar -zxvf neo4j-community-3.5.28-unix.tar.gz && \
	mv neo4j-community-3.5.28 neo4j && \
	rm -rf neo4j-community-3.5.28-unix.tar.gz 

# 下载插件graph和apoc，并放置在plugins目录下
RUN wget -P /home/neo4j/plugins \
	https://github.com/neo4j-contrib/neo4j-graph-algorithms/releases/download/3.5.4.0/graph-algorithms-algo-3.5.4.0.jar && \
	wget -P /home/neo4j/plugins \
	https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/3.5.0.15/apoc-3.5.0.15-all.jar

# 拷贝neo4j.conf的配置文件
COPY neo4j.conf /home/neo4j/conf/neo4j.conf 

#将copd-neo4j的数据库拷贝进去
RUN mkdir /home/neo4j/data/databases/copd-db
COPY copd-db /home/neo4j/data/databases/copd-db/

#将TC-shiny拷贝进去----------------------------------------------------------------------------------
RUN rm -rf /home/rstudio/shinyApps/*
COPY copd_shiny /home/rstudio/shinyApps/

# 将neo4j目录下所有文件的权限修改
RUN chmod -R 777 /home/neo4j/*

# 设置可以与容器外文件系统挂钩的目录
VOLUME /home/neo4j/data /home/rstudio/shinyApps

# 设置工作目录
WORKDIR /home

# 将supervisor-neo4j.conf文件拷贝到相应的位置，以便supervisor对neo4j进行控制-------------------
COPY supervisor-neo4j.conf /etc/supervisor/conf.d/neo4j.conf

# 端口号 3838是shiny server, 7474 7473 7687 是neo4j, 8787 是rstudio
EXPOSE 3838 7474 7473 7687 8787

# 最后，统一使用supervisor来管理shiny-server、neo4j rstudio-server
RUN echo user=root >>  /etc/supervisor/supervisord.conf
CMD ["/usr/bin/supervisord","-n"]
