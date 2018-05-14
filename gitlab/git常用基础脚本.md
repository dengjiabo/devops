# git常用基础脚本

1、克隆项目

git clone PASTE HTTPS OR SSH HERE

2、创建一个目录

mkdir NAME-OF-YOUR-DIRECTORY

3、在目录中创建文件

```
touch README.md
nano README.md
#### ADD YOUR INFORMATION
#### Press: control + X
#### Type: Y
#### Press: enter
```

4、删除文件

rm NAME-OF-FILE

5、删除目录及其所有内容

rm -rf NAME-OF-DIRECTORY

6、查看命令行中的历史记录

history

7、检查git是否安装或者查看git版本

git --version

8、git设置用户名和邮箱

git config --global user.name "YOUR_USERNAME"

git config --global user.email "your_email_address@example.com"

验证用户名和邮箱

git config --global user.name

git config --global user.email

9、切换到主分支

git checkout master

10、下载项目中的最新修改

git pull REMOTE NAME-OF-BRANCH -u

11、创建一个分支

git checkout -b NAME-OF-BRANCH

12、切换到工作分支， 分支的名字不能有空格，你可以使用连字符或下划线

git checkout NAME-OF-BRANCH

13、查看你所做的修改

git status

14、添加更改和提交

git add CHANGES IN RED

git commit -m "DESCRIBE THE INTENTION OF THE COMMIT"

15、推送到gitlab仓库

git push REMOTE NAME-OF-BRANCH

说明：将本地代码提交到远端服务器，origin表示远端默认服务器，在“git clone”时已指明，第一个develop_lsyer表示本地的分支名，第二个develop_lsyer表示远端分支名，整条命令表示将本地的develop_lsyer分支代码修改情况，merge到远端的develop_lsyer分支

16、删除git存储库中的所有修改，但保留未被跟踪的内容

git checkout .

17、删除git存储库中的所有修改，包括未跟踪的内容

git clean -f

18、将分支和主分支合并

git checkout NAME-OF-BRANCHgit merge master

19、将主分支和分支合并

git checkout mastergit merge NAME-OF-BRANCH

























