# docker常用基础脚本

1、查看docker版本

docker --version

2、拉取docker镜像

docker pull tomcat

3、运行docker镜像

docker run -ti --name tomcat -p 58080:8080 image-id

--name  别名

--p  端口映射

4、查看docker运行状况

docker ps		--查看正在运行的docker

docker ps -a		--查看所有的docker，无论正在运行与否

5、进入docker容器

docker exec -ti tomcat /bin/bash

6、查看镜像的详细信息

docker inspect imageID

7、停止运行中的docker容器

docker stop imageID

8、删除docker容器

docker rm imageID

9、删除镜像

docker rmi imagesID

10、保存镜像	（save、load需要通过移动设备来copy，违背docker初衷，一般不这么做）

docker save -o k8s-dns-dnsmasq-nanny-amd64.tar k8s.gcr.io/k8s-dns-dnsmasq-nanny-amd64:1.14.8

11、加载镜像

docker load k8s-dns-dnsmasq-nanny-amd64.tar

12、修改镜像标签（上传镜像到harbor过程中需要使用）

docker tag tomcat ip/library/tomcat:1.0	--tomcat为原有标签，ip/library/tomcat:1.0为修改后的标签

13、push镜像到harbor私库

docker push ip/library/tomcat:1.0

