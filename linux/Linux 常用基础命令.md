#Linux 常用基础命令

1、通过man 命令来查找bash手册

man vim	--查看vim的帮助手册

另外也可以通过 info vim, vim --help, vim -help 等命令来查看帮助手册

2、通过file命令查看文件类型

file test( 具体文件或者目录名称 )	

3、tail命令和head命令

tail -n 500 log  （查看log文件的最后500行）

head -n 500 log （查看log文件的前500行）

4、查看是否安装某软件

rpm -qa|grep tomcat		--查看是否安装tomcat

上面的命令其实是由两条命令够成，中间由管道隔开，从而组成了一条命令：其中rpm -qa可以查看所有的linux中已经安装的所有软件grep nfs则是以上一句脚本的输出作为输入来进行查询，获取所有和nfs相关的软件信息

5、卸载某软件

rmp -e 前一句命令查询到的软件名		--一般性卸载

rpm -e --nodeps 上面查询到的软件名 	--强制卸载

6、查看端口使用情况

netstat -tunlp

7、通过命令直接修改hostname

hostnamectl set-hostname master  ( 直接将主机名字修改成master )		--也可以通过修改/etc/hosts来完成

8、查看进程

ps -l		--查看虚拟机进程状态

O代表正在运行； S代表在休眠； R代表可运行，正等待运行； Z代表僵化，进程已结束但父进程已不存在； T代表停止PRI：进程的优先级（越大的数字代表越低的优先级）

9、top 命令实时显示进程信息（按q退出）

top命令的输出中将进程叫作任务（task）：有多少进程处在运行、休眠、停止或是僵化状态D代表可中断的休眠状态， R代表在运行状态， S代表休眠状态， T代表跟踪状态或停止状态， Z代表僵化状态

10、mount 命令输出当前 系统上挂载的设备列表

mount命令提供四部分信息： 1、媒体的设备文件名 2、媒体挂载到虚拟目录的挂载点 3、文件系统类型 4、已挂载媒体的访问状态

手动将U盘/dev/sdb1挂载到/media/disk                mount -t vfat /dev/sdb1 /media/disk

11、使用 df 命令查看磁盘空间的使用情况

df -h

12、查看内存使用情况

free -h

13、查看历史命令

history		--通过该命令可以查看一段时间以前在linux虚机中执行过的命令

命令历史记录被保存在隐藏文件.bash_history中，它位于用户的主目录中。这里要注意的是，bash命令的历史记录是先存放在内存中，当shell退出时才被写入到历史文件中。( 可以通过history -a 将命令历史记录强行写入bash_history文件中 )

14、文件权限说明

ls 查看文件是，以 d 开头的表示文件目录，以 - 开头的表示文件

文件权限分为三种，分别是rwx

 u代表用户 g代表组 o代表其他 a代表上述所有

chmod ugoa -+ rwx 文件名		--赋予文件权限	7表示某个用户有所有权限