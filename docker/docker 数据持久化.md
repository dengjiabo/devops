# docker 数据持久化

###使用卷来完成持久化     （ Type: volume ）

1、新建卷 volume
docker volume create devtest     --devtest是卷名称

2、docker volume ls                   --查看服务器中的所有卷

3、docker volume inspect devtest     --查看卷的基本信息

4、启动docker命令
docker run -d --name tomcat -v devtest:/data tomcat:latest      --name参数指定别名，  -v 表示将卷的文件和容器中的/data目录进行同步

```
docker run -d \                                                  --使用只读卷
  --name=nginxtest \
  -v nginx-vol:/usr/share/nginx/html:ro \
  nginx:latest
```

docker exec -ti tomcat /bin/bash   --进入到容器之后，进入到/data目录，创建一个文件。同理是可以在docker卷的相应目录查看到对应文件

docker volume对应默认目录为： /var/lib/docker/volume/volume名称

5、docker inspect devtest              --验证创建卷并安装成功（找到mount部分）

6、删除卷

docker volume rm

docker volume prune               --删除所有没有使用的本地卷

### 直接使用绑定挂载来完成持久化  （ Type: bind ） 

docker 启动脚本

docker run -d   -it   --name devtest   -v "$(pwd)"/target:/app   nginx:latest     --使用docker inspect devtest验证绑定安装正确创建。寻找Mounts部分：可以发现绑定类型为bind

如果是同一个宿主机上的不同docker需要进行数据共享，那么可以直接挂载相同的目录到不同的容器即可，注意代码处理好并发问题

如果是不同宿主机，则可以使用分布式数据卷驱动，让分布在不同宿主的容器都可以访问到的分布式存储的位置。如S3之类：<https://docs.docker.com/engine/extend/plugins/#volume-plugins>

------

1、数据容器、数据卷、命名卷、匿名卷、挂载目录这些都有什么区别？

​	首先，挂载分为挂载本地宿主目录 和 挂载数据卷(Volume)。而数据卷又分为匿名数据卷和命名数据卷。
绑定宿主目录的概念很容易理解，就是将宿主目录绑定到容器中的某个目录位置。这样容器可以直接访问宿主目录的文件。其形式是docker run -d -v /var/www:/app nginx这里注意到 -v 的参数中，前半部分是绝对路径。在 docker run 中必须是绝对路径，而在 docker-compose 中，可以是相对路径，因为 docker-compose 会帮你补全路径。

​	另一种形式是使用 Docker Volume，也就是数据卷。这是很多看古董书的人不了解的概念，不要跟数据容器（Data Container）弄混。数据卷是 Docker 引擎维护的存储方式，使用 docker volume create 命令创建，可以利用卷驱动支持多种存储方案。其默认的驱动为 local，也就是本地卷驱动。本地驱动支持命名卷和匿名卷。
顾名思义，命名卷就是有名字的卷，使用 docker volume create --name xxx 形式创建并命名的卷；而匿名卷就是没名字的卷，一般是 docker run -v /data 这种不指定卷名的时候所产生，或者 Dockerfile 里面的定义直接使用的。有名字的卷，在用过一次后，以后挂载容器的时候还可以使用，因为有名字可以指定。所以一般需要保存的数据使用命名卷保存。

​	而匿名卷则是随着容器建立而建立，随着容器消亡而淹没于卷列表中（对于 docker run 匿名卷不会被自动删除）。对于二代 Swarm 服务而言，匿名卷会随着服务删除而自动删除。 因此匿名卷只存放无关紧要的临时数据，随着容器消亡，这些数据将失去存在的意义。
	此外，还有一个叫数据容器 (Data Volume) 的概念，也就是使用 --volumes-from 的东西。这早就不用了，如果看了书还在说这种方式，那说明书已经过时了。按照今天的理解，这类数据容器，无非就是挂了个匿名卷的容器罢了。在 Dockerfile 中定义的挂载，是指 匿名数据卷。Dockerfile 中指定 VOLUME 的目的，只是为了将某个路径确定为卷。
	我们知道，按照最佳实践的要求，不应该在容器存储层内进行数据写入操作，所有写入应该使用卷。如果定制镜像的时候，就可以确定某些目录会发生频繁大量的读写操作，那么为了避免在运行时由于用户疏忽而忘记指定卷，导致容器发生存储层写入的问题，就可以在 Dockerfile 中使用 VOLUME 来指定某些目录为匿名卷。这样即使用户忘记了指定卷，也不会产生不良的后果。

​	这个设置可以在运行时覆盖。通过 docker run 的 -v 参数或者 docker-compose.yml 的 volumes 指定。使用命名卷的好处是可以复用，其它容器可以通过这个命名数据卷的名字来指定挂载，共享其内容（不过要注意并发访问的竞争问题）。
	比如，Dockerfile 中说 VOLUME /data，那么如果直接 docker run，其 /data 就会被挂载为匿名卷，向 /data 写入的操作不会写入到容器存储层，而是写入到了匿名卷中。但是如果运行时 docker run -v mydata:/data，这就覆盖了 /data 的挂载设置，要求将 /data 挂载到名为 mydata 的命名卷中。所以说 Dockerfile 中的 VOLUME 实际上是一层保险，确保镜像运行可以更好的遵循最佳实践，不向容器存储层内进行写入操作。
	数据卷默认可能会保存于 /var/lib/docker/volumes，不过一般不需要、也不应该访问这个位置。

2、卷和挂载目录有什么区别？

​	卷 (Docker Volume) 是受控存储，是由 Docker 引擎进行管理维护的。因此使用卷，你可以不必处理 uid、SELinux 等各种权限问题，Docker 引擎在建立卷时会自动添加安全规则，以及根据挂载点调整权限。并且可以统一列表、添加、删除。另外，除了本地卷外，还支持网络卷、分布式卷。

​	而挂载目录那就没人管了，属于用户自行维护。你就必须手动处理所有权限问题。特别是在 CentOS 上，很多人碰到 Permission Denied，就是因为没有使用卷，而是挂载目录，而且还对 SELinux 安全权限一无所知导致。