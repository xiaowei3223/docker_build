# 介绍
DynBioNet 是一个shinyApp，用于用户能够直观地展现自己的网络图而做的。代码在https://github.com/mora-lab/DynBioNet
这里是为其做一个docker镜像，以便能够在docker中使用。

# 运行所创建的docker镜像
```shell
sudo docker run -d \
    --publish=3838:3838 \
    --name dynbionet \
     moralab/dynbionet:latest
```
运行后，然后打开http://localhost:3838/DynBioNet/ 即可查看DynBioNet的shiny App

# 创建docker镜像
【注意】 在当前文件夹下运行下面的命令
## Step1: 下载DynBioNet shiny App 并压缩

```shell
#下载DynBioNet
wget -O DynBioNet.tar.gz https://github.com/mora-lab/DynBioNet/archive/master.tar.gz && \
	tar -zxvf DynBioNet.tar.gz && \
	rm -rf DynBioNet.tar.gz && \
	mv DynBioNet-master DynBioNet 

# 下载kegg_GO_info.sqlite 
mkdir -p DynBioNet/data/databases/kegg_GO_info && \
wget -O DynBioNet/data/databases/kegg_GO_info/kegg_GO_info.sqlite https://zenodo.org/record/5336148/files/kegg_GO_info.sqlite?download=1 
	
# 下载COPD-data.sqlitels
mkdir -p DynBioNet/data/databases/user_data && \
wget -O DynBioNet/data/databases/user_data/COPD-data.sqlite https://zenodo.org/record/5336148/files/COPD-data.sqlite?download=1

# 压缩成一个包(为了能够直接拷贝进docker镜像中)
tar -zcvf DynBioNet-full.tar.gz DynBioNet
```

## Step2: 创建docker镜像
```shell
# 创建docker镜像
sudo docker build -t moralab/dynbionet:latest .
```

## Step3: test docker镜像
```shell
#运行该docker镜像-后台-拷贝文件
sudo docker run -d \
    --publish=3838:3838 \
    --name dynbionet \
     moralab/dynbionet:latest
```	 
运行后，然后打开http://localhost:3838/DynBioNet/ 即可查看DynBioNet的shiny App

## Step4: 删除dynbionet容器
```shell
# 删除容器
sudo docker rm -f dynbionet
```

## Step5: 上传docker镜像到dockerhub
```shell
#登录账号
sudo docker login -u 'moralab' -p 'password'
#上传容器
sudo docker push moralab/dynbionet:latest
#退出账号
sudo docker logout
```

