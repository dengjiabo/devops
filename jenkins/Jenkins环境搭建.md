# Jenkins环境搭建

###安装Jenkins

1、安装jdk

jdk下载地址：（如果是阿里云服务器可以直接使用，如果不是则需要删除中间的-internal）

<https://hand-123.oss-cn-shanghai-internal.aliyuncs.com/jdk-8u161-linux-x64.tar.gz>

jdk环境变量配置：

```
export JAVA_HOME=/usr/share/jdk1.6.0_14 （这个路径是进入到jdk所在目录pwd获取）
export PATH=$JAVA_HOME/bin:$PATH 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
```

修改了上面的配置文件之后，需要执行source /etc/profile让配置文件生效

2、tomcat下载地址：

<https://hand-123.oss-cn-shanghai-internal.aliyuncs.com/apache-tomcat-8.5.29.tar.gz>

将下载好的包解压之后，进入到bin目录下启动tomcat即可

3、Jenkins下载链接：

<http://mirrors.jenkins.io/war-stable/2.89.2/jenkins.war>

将下载的Jenkins war包直接放置到webapp目录下即可



### 配置Jenkins

1、配置安装各种插件（git、maven、jdk、docker）

进入到Jenkins系统管理、全局工具配置，将安装的各种路径配置到对应的地方即可举例：

运行mav -v 可以获取到mavne的HOME_Path，然后配置到Jenkins的环境变量中

2、在Jenkins中进行参数化构建

在创建Jenkins job工程的时候，可以在General中勾选参数化构建。参数化构建过程中可以选择git参数，从而实现直接拉取git的分支代码，这个功能需要先在Jenkins插件中安装 git 参数化的插件，然后才能有git相关选项。

