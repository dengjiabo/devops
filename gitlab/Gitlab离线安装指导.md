# Gitlab离线安装指导

1、安装依赖项

openssh-server二进制安装包

下载链接：https://centos.pkgs.org/7/centos-x86_64/openssh-server-7.4p1-11.el7.x86_64.rpm.html

2、下载gitlab安装包

下载链接：curl -O https://downloads-packages.s3.amazonaws.com/centos-6.6/gitlab-ce-7.10.0~omnibus.2-1.x86_64.rpm

3、安装步骤

（1）、rpm -ivh gitlab-ce-7.10.0~omnibus.2-1.x86_64.rpm

（2）、安装好上面的rpm包之后，会在/etc 目录下生成gitlab文件夹，需要修改其中的配置文件

​	vim /etc/gitlab/gitlab.rb  （修改最上面的访问地址为对应服务器的ip地址即可）

（3）、修改gitlab访问端口（gitlab默认使用80端口，避免和harbor出现冲突）

​	cd /var/opt/gitlab/nginx/conf

​	vim gitlab-http.conf   ( 修改其中的监听端口为8083 )

​	gitlab-ctl restart  （ 重启gitlab，随后在浏览器中通过http://ip+8083进行访问 ）

4、安装说明

​	如果在安装gitlab rpm包的过程中卡在  ruby_block[supervise_redis_sleep\] action run 

这是因为gitlab需要的一个服务没有启动，执行下面的脚本启动服务再次安装

（1）、按住CTRL+C强制结束；

（2）、运行：sudo systemctl restart gitlab-runsvdir

（3）、再次执行：sudo gitlab-ctl reconfigure