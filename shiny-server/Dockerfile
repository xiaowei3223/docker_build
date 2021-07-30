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

# 安装RStudio ------------------------------------------------------------------------------
# WORKDIR /home/
RUN apt-get -y install gdebi-core && \
	wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-1.4.1717-amd64.deb && \
	gdebi -n rstudio-server-1.4.1717-amd64.deb && \
	rm -rf rstudio-server-1.4.1717-amd64.deb

# 为rstudio创建用户rstudio, 该用户密码是rstudio
RUN useradd -m -d /home/rstudio -g rstudio-server rstudio && \
    echo rstudio:rstudio | chpasswd 

# shiny-server.conf中改动了用户和shinyApps的根目录
COPY shiny-server.conf /etc/shiny-server/shiny-server.conf

# 创建shinyApps来存放shiny应用程序
RUN mkdir /home/rstudio/shinyApps 

# 设置工作目录
WORKDIR /home/rstudio/shinyApps

# 将example shiny app拷贝到shinyApps目录下。
COPY exampleApp/* /home/rstudio/shinyApps/
RUN mkdir /home/rstudio/shinyApps/sampleApp && \
	mv server.R  /home/rstudio/shinyApps/sampleApp && \
	mv ui.R /home/rstudio/shinyApps/sampleApp && chmod -R 777 /home/rstudio/shinyApps

# 设置可以与容器外文件系统挂钩的目录
VOLUME /home/rstudio/shinyApps

# 端口号 3838是shiny server，878是rstudio-server
EXPOSE 3838 8787

# 最后，统一使用supervisor来管理shiny-server、rstudio-server
RUN mkdir -p /var/log/supervisor
COPY supervisor-shiny-server.conf /etc/supervisor/conf.d/shiny-server.conf
# 将supervisor-rstudio-server.conf文件拷贝到相应的位置，以便supervisor对rstudio-server进行控制
COPY supervisor-rstudio-server.conf /etc/supervisor/conf.d/rstudio-server.conf
RUN echo user=root >>  /etc/supervisor/supervisord.conf
CMD ["/usr/bin/supervisord","-n"]

