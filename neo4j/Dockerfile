# 下载ubuntu:18.04
FROM ubuntu:18.04

# 一般配置
RUN apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* && \
    localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

# 安装JAVA ------------------------------------------------------------------------
RUN apt-get update && \
	apt-get install -y default-jre default-jdk 


# 安装wget
RUN apt-get install -y wget

# 安装neo4j --------------------------------------------------------------------------------------
WORKDIR /home/
RUN wget -O neo4j-community-3.5.28-unix.tar.gz https://neo4j.com/artifact.php?name=neo4j-community-3.5.28-unix.tar.gz && \
	tar -zxvf neo4j-community-3.5.28-unix.tar.gz && \
	mv neo4j-community-3.5.28 neo4j && \
	rm -rf neo4j-community-3.5.28-unix.tar.gz \
	rm -rf neo4j/conf/neo4j.conf 

COPY neo4j.conf /home/neo4j/data/neo4j.conf

RUN ln /home/neo4j/data/neo4j.conf /home/neo4j/conf/neo4j.conf 

# 下载插件graph和apoc，并放置在plugins目录下
RUN cd /home/neo4j/plugins && \
	wget https://github.com/neo4j-contrib/neo4j-graph-algorithms/releases/download/3.5.4.0/graph-algorithms-algo-3.5.4.0.jar && \
	wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/3.5.0.15/apoc-3.5.0.15-all.jar && \
	cd /home

# 将home目录下所有文件的权限修改
RUN chmod -R 777 /home

# 设置工作目录
WORKDIR /home/neo4j

# 设置可以与容器外文件系统挂钩的目录
VOLUME /home/neo4j/data

# 端口号 7474 7473 7687 是neo4j
EXPOSE 7474 7473 7687

# 最后，统一使用supervisor来管理shiny-server、neo4j rstudio-server
RUN apt-get install -y supervisor
RUN mkdir -p /var/log/supervisor
COPY supervisor-neo4j.conf /etc/supervisor/conf.d/neo4j.conf
#CMD ["/usr/bin/supervisord"]
RUN echo user=root >>  /etc/supervisor/supervisord.conf
CMD ["/usr/bin/supervisord","-n"]

