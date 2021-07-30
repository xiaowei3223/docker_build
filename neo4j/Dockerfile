# 下载ubuntu:18.04
FROM ubuntu:18.04

#  作者信息
MAINTAINER Mora-Lab <moralabgalaxy@163.com>

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

# 安装supervisor进程管理软件
RUN apt-get install -y supervisor

# 设置配置文件
COPY neo4j.conf /home/neo4j/conf/neo4j.conf

# 将neo4j目录下所有文件的权限修改
RUN chmod -R 777 /home/neo4j/*

# 设置工作目录
WORKDIR /home/neo4j

# 设置可以与容器外文件系统挂钩的目录
VOLUME /home/neo4j/data

# 端口号 7474 7473 7687 是neo4j
EXPOSE 7474 7473 7687

# 最后，统一使用supervisor来管理neo4j
RUN mkdir -p /var/log/supervisor
COPY supervisor-neo4j.conf /etc/supervisor/conf.d/neo4j.conf
#CMD ["/usr/bin/supervisord"]
RUN echo user=root >>  /etc/supervisor/supervisord.conf
CMD ["/usr/bin/supervisord","-n"]
