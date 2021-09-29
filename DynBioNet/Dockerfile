# 下载ubuntu:18.04
FROM ubuntu:18.04

#  作者信息
MAINTAINER Mora-Lab <moralabgalaxy@163.com>

# 一般配置
RUN apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* && \
    localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

# 安装R -----------------------------------------------------------------------------------
RUN apt update -qq && \
	apt install --no-install-recommends -y software-properties-common dirmngr && \
	apt-get install -y wget && \
	wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc && \
	add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/" && \
	apt install --no-install-recommends -y r-base 

# 安装shiny-server ------------------------------------------------------------------------
RUN apt-get install -y gdebi-core \
    default-jre \
    default-jdk \
    subversion  \
    libgdal-dev \
    libproj-dev  \
    language-pack-pt-base \
    libcurl4-gnutls-dev  \
    libv8-dev \
    git \
    libxml2-dev \
    gfortran \
    libpcre2-dev \
    libbz2-dev \
    zlib1g-dev

# XXX Why javareconf needs to be used 2 times?
RUN R CMD javareconf

# Install R packages
RUN R -e "install.packages('shiny', repos='http://cran.rstudio.com/')"  && \
    R -e "install.packages('rmarkdown', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('rjson', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('maps', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('raster', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('dismo', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('shinythemes', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('rgdal', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('devtools', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('rgbif', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('shinydashboard', repos='http://cran.rstudio.com/')" && \ 
    R -e "install.packages('randomForest', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('kernlab', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('rJava', repos='http://cran.rstudio.com/')" && \
    R -e "options(repos='http://cran.rstudio.com/'); devtools::install_github('rstudio/leaflet')"

# Configuring R to use installed Oracle Java
RUN R CMD javareconf

# 下载和安装shiny server
WORKDIR /home/
RUN wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.16.958-amd64.deb && \
	gdebi -n shiny-server-1.5.16.958-amd64.deb && \
	rm shiny-server-1.5.16.958-amd64.deb

# 安装进程管理软件supervisor-----------------------------------------------------------------
RUN apt-get install -y supervisor

# 安装DynBioNet需要的R包------------------------------------------------
RUN R -e "install.packages('BiocManager', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('visNetwork', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('ggalluvial', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('ggplot2', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('DT', repos='http://cran.rstudio.com/')"  && \
    R -e "install.packages('igraph', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('xlsx', repos='http://cran.rstudio.com/')" && \
 	R -e "install.packages('tidyverse', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('dplyr', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('readr', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('tidyr', repos='http://cran.rstudio.com/')"  && \
    R -e "install.packages('RColorBrewer', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('RSQLite', repos='http://cran.rstudio.com/')" && \
    R -e "install.packages('downloader', repos='http://cran.rstudio.com/')"

# 设置工作目录---
WORKDIR /srv/shiny-server

# 拷贝dynbioNet文件
COPY DynBioNet-full.tar.gz /srv/shiny-server/DynBioNet-full.tar.gz

# 解压DynBioNet-full.tar.gz 
RUN cd /srv/shiny-server && tar -zxvf DynBioNet-full.tar.gz && rm -rf DynBioNet-full.tar.gz 

# 设置可以与容器外文件系统挂钩的目录
VOLUME /srv/shiny-server

# 端口号 3838是shiny server
EXPOSE 3838 8787

# 最后，统一使用supervisor来管理shiny-server、rstudio-server
RUN mkdir -p /var/log/supervisor

# 将supervisor-shiny-server.conf文件拷贝到相应的位置，以便supervisor对shiny-server进行控制
COPY supervisor-shiny-server.conf /etc/supervisor/conf.d/shiny-server.conf
RUN echo user=root >>  /etc/supervisor/supervisord.conf
CMD ["/usr/bin/supervisord","-n"]

