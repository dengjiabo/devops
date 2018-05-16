# maven基础命令

在通过Jenkins完成自动化部署的时候，实际上的代码打包还是通过maven来完成的。

安装方式：在虚拟机联网的情况下，可以直接通过

yum install maven 来进行安装

通过mav -v 检测maven是否安装成功，以及获取maven版本

mvn clean                --清空项目

mvn compile          --编译源代码（如果是第一次编译的话需要联网，maven需要下载各种jar包）

mvn -U package -P dev -DskipTests             --将编译过后的源代码打成war包或者jar包 （打包成功之后会发现项目中多了一个target目录，打包好的压缩包就存放在这个目录中）

注：打包是需要将代码通过git拉取之后才能进行