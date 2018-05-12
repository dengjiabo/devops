# Harbor 离线安装指导

1、harbor离线安装包下载链接：

https://github.com/vmware/harbor/blob/master/docs/installation_guide.md

2、harbor依赖项安装

（1）、docker离线安装

下载需要的离线安装软件包，解压之后会得到docker安装包以及相关的依赖项，按照顺序和要求安装相应的rpm包即可将docker安装成功docker 

软件包下载链接：

外网：<https://hand-123.oss-cn-shanghai.aliyuncs.com/tools/docker/docker.tar.gz>

内网：[https://hand-123.oss-cn-shanghai-internal.aliyuncs.com/tools/docker/docker.tar.gz](https://hand-123.oss-cn-shanghai.aliyuncs.com/tools/docker/docker.tar.gz)

（2）、docker-compose离线安装

下载docker-compose的可执行插件，然后赋予docker-compose文件可执行权限即可

docker-compose离线安装指南：

<https://docs.docker.com/compose/install/#install-compose>

（3）、harbor还有可能需要依赖项：python和openssl，但是一般的虚拟机都已经有所以就没有提供安装方法

3、安装步骤

（1）、解压下载好的harbor离线安装包

（2）、配置harbor.cfg文件中的hostname属性

（3）、./install.sh  运行脚本安装harbor。运行成功后会自动启动harbor

4、将harbor的访问方式从http修改成https

参考链接：https://github.com/vmware/harbor/blob/master/docs/configure_https.md

（1）、分别执行下面的脚本生成证书文件  注：在两次脚本执行过程中输入尽量保持一致

```
  openssl req \
    -newkey rsa:4096 -nodes -sha256 -keyout ca.key \
    -x509 -days 365 -out ca.crt
    
  openssl req \
    -newkey rsa:4096 -nodes -sha256 -keyout hand.key \
    -out hand.csr
    
  echo subjectAltName = IP:192.168.1.101 > extfile.cnf    --注意修改对应ip
  
  openssl x509 -req -days 365 -in hand.csr -CA ca.crt -CAkey ca.key -CAcreateserial -extfile extfile.cnf -out hand.crt
```

（2）、修改harbor.cfg配置文件

​	执行 docker-compose down 停止harbor

​	修改配置文件: 

​	ui_url_protocol = http  改为 ui_url_protocol = https

​	ssl_cert = /data/cert/xxx.crt  改为 ssl_cert = /root/cert/hand.crt （证书文件所在的绝对路径）

​	ssl_cert_key = /data/cert/xxx.key 改为 ssl_cert_key = /root/cert/hand.key

（3）、执行 ./prepare 加载配置文件信息，执行 docker-compose up -d 启动harbor



# Harbor常用基础脚本

由于在安装过程中是通过docker-compose来完成的，所以启停过程中也是需要通过docker-compose来完成

1、停止

docker-compose down

2、启动

docker-compose up -d

3、每次在修改过配置文件之后，都需要运行./prepare加载配置文件