

# 在IntelliJ IDEA中使用 GIT

**首先安装好git之后**

Git 各平台安装包下载地址为：<http://git-scm.com/downloads>

**打开Idea File—> Setting** 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\GitSetting.png)

**设置Git exe 如下图,设置好之后点Test，跳出successful就是成功了** 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\设置Git.exe.png)

**完成之后** 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\Git克隆项目.md)

**就可以从github上clone项目了** 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\Git克隆项目2.png)

# **如何将自己的项目传到Github上？**

首先自己在IDEA上创建好一个项目之后 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\上传到Git.png)

Share好之后可以看到自己的Github上会出现对应的项目

然后import into version control –>creat git repository创建一个本地仓库 
一般创在项目下 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\创建本地仓库.png)

创建好之后目录下会出现一个隐藏的.git文件 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\本地仓库.png)

下图是Git与提交有关的三个命令对应的操作， 
**Add命令是把文件从IDE的工作目录添加到本地仓库的stage区** 
**Commit命令把stage区的暂存文件提交到当前分支的仓库，并清空stage区。** 
**Push命令把本地仓库的提交同步到远程仓库。** 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\仓库连接图.png)

如果你的项目有改动，先点add将全部改动上传到stage区， 
然后再点commit，将想上传的文件传到分支中去 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\上传1.png)

选好自己需要上传的文件，写好注释，点commit and push 

![è¿éåå¾çæè¿°](D:\java\md_pictures\Git\上传2.png)

然后再直接点push，就将改动上传到Github中了。