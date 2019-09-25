# 使用Xshell查看Linux服务器日志

​	在Linux日常运用中经常会使用到服务器的日志，服务器的日志可以帮助我们调试服务器程序，也可以打印出错误日志，看到用户访问日志，帮助我们定位问题，那么有用户问：怎样才能查看Linux服务器日志？

​	当开发使用的是Windows操作系统，就可以使用Linux的远程连接客户端Xshell进行连接，具体查看步骤如下：

## 具体操作步骤

1.双击打开**Xshell**终端模拟器，在打开的主界面窗口中，点击左上角新建按钮，如下图所示；

![æ°å"ºä¼è¯](D:\java\md_pictures\Xshell\Xshell-1.png)

2.点击新建后，如下图所示，需要与自己的Linux服务器建立远程连接，在连接中输入需要链接的服务器IP地址和主机名进行连接；

![è¿æ¥æå¡å¨](D:\java\md_pictures\Xshell\Xshell-2.png)

3.通过cd命令，进入服务器日志文件所存放的目录，比如这里是log目录；

![æ¥çæ¥å¿ç®å½](D:\java\md_pictures\Xshell\Xshell-3.png)

4.进入log目录，找到需要监控的日志文件，比如需要监控production.log这个日志文件；

![æ¥å¿æä"¶](D:\java\md_pictures\Xshell\Xshell-4.png)

5.使用命令：tail -f log/production.log 查看新追加到文档中的信息，这样可以实时看到自己的访问日志，便于自己调试程序和查看错误。

![æ¥çææ¡£ä¿¡æ¯](D:\java\md_pictures\Xshell\Xshell-5.png)

## 常用命令

**第一个命令：ls -l**  简写为**ll**

ls全称是list，列出指定路径下的所有文件，-l表示显示文件的完整属性。

![æä"¶å½ä"¤](D:\java\md_pictures\Xshell\Xshell-Linux-1.png)

**第二个命令：pwd** 

表示显示当前所在的目录，当我们操作很多目录之前不知道当前目录所在时就可以用这个命令来查看。

![ç®å½å½ä"¤](D:\java\md_pictures\Xshell\Xshell-Linux-2.png)

**第三个命令：三个管理时钟的命令**

时钟分为硬件时钟和系统时钟，两个时间很可能不一致。date命令看到的是系统时钟，而其他两个则是硬件时钟。

![æ¶éå½ä"¤](D:\java\md_pictures\Xshell\Xshell-Linux-3.png)

**第四个命令：help**

可以进行命令的执行帮助，还可以用man命令，直接查看命令的手册，不过help命令更加常用。

![å¸®å©å½ä"¤](D:\java\md_pictures\Xshell\Xshell-Linux-4.png)

**第五个命令：passwd**

用来修改当前的密码，输入这个密码后换行，然后重复输入两次新的密码即可完成密码的修改，如果不想修改了用快捷键Ctrl+c可以终止命令。

![å¯ç å½ä"¤](D:\java\md_pictures\Xshell\Xshell-Linux-5.png)

**第六个命令： shutdown -h now**

表示立即关机，这样我们就可以直接关闭Linux系统啦。

![å³é­ç³"ç"](D:\java\md_pictures\Xshell\Xshell-Linux-6.png)