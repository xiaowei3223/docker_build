# 下载ubuntu:18.04
FROM ubuntu:18.04

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
RUN apt-get install -y gdebi-core 
RUN apt-get install -y default-jre
RUN apt-get install -y default-jdk
RUN apt-get install -y subversion 
RUN apt-get install -y libgdal-dev
RUN apt-get install -y libproj-dev 
RUN apt-get install -y language-pack-pt-base 
RUN apt-get install -y libcurl4-gnutls-dev 
RUN apt-get install -y libv8-dev

# 这些软件是因为安装R包时所需要的
RUN apt install -y git
RUN apt-get install -y libxml2-dev 
RUN apt-get install -y gfortran 
RUN apt-get install -y libpcre2-dev 
RUN apt-get install -y libbz2-dev 
RUN apt-get install -y zlib1g-dev

# XXX Why javareconf needs to be used 2 times?
RUN R CMD javareconf

# Install R packages
RUN R -e "install.packages('shiny', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('rmarkdown', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('rjson', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('maps', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('raster', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('dismo', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('shinythemes', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('rgdal', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('devtools', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('rgbif', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('shinydashboard', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('randomForest', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('kernlab', repos='http://cran.rstudio.com/')" 
RUN R -e "install.packages('rJava', repos='http://cran.rstudio.com/')" 
RUN R -e "options(repos='http://cran.rstudio.com/'); devtools::install_github('rstudio/leaflet')"

# Configuring R to use installed Oracle Java
RUN R CMD javareconf

# 下载和安装shiny server
WORKDIR /home/
RUN wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.16.958-amd64.deb && \
	gdebi -n shiny-server-1.5.16.958-amd64.deb && \
	rm shiny-server-1.5.16.958-amd64.deb

# 设置软链接,以后只需要将shinyAPP放置/home/shiny/shinyApp目录下就可以了。
RUN mkdir /home/shiny/shinyApp && \
	ln -s /home/shiny/shinyApp /srv/shiny-server/
    
# 将home目录下所有文件的权限修改
RUN chmod -R 777 /home

# 设置工作目录
WORKDIR /home/shiny/shinyApp

# 设置可以与容器外文件系统挂钩的目录
VOLUME /home/shiny/shinyApp

# 最后，统一使用supervisor来管理shiny-server、neo4j rstudio-server
RUN apt-get install -y supervisor
RUN mkdir -p /var/log/supervisor
COPY shiny-server.conf /etc/supervisor/conf.d/shiny-server.conf

# 端口号 3838是shiny server
EXPOSE 3838 

#CMD ["/usr/bin/supervisord"]
RUN echo user=root >>  /etc/supervisor/supervisord.conf
CMD ["/usr/bin/supervisord","-n"]


