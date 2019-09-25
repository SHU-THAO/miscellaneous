# Linux入门

## 常用命令【重点】

### 磁盘管理命令

**ls命令：列出目录内容**

参数：

​        -a 查询所有文件和文件夹。包含隐藏的

​        -l 查询详细列表    ls -l   简写为**ll**

​        -h 友好展示信息   **ll -h**

​			d目录，-普通文件, l链接

需求：展示某个目录下的内容    ls 目录名

所在位置：/root

想要查看:/etc目录下的内容	命令：ll -h /etc



**cd(change directory) 功能：切换目录**

 **cd ../**    **，向上一层目录**

cd /      ，切换到Linux的根目录

 **cd /****目录名** **，** **任意目录** **切换到指定目录中**     /目录名  Linux的绝对路径

​				windows:  c:/xxxx/xxx.jpg

 **cd    ~**      ，切换到当前用户家目录。Root用户家目录 /root    **回家**

 cd   -       ，切换到上一次访问的目录。   只能两个目录相互切换

**Linux绝对**：  cd /etc/x1     先切换到/目录，然后在/目录中找到子目录etc，在etc中找子目录x1

​                         在任意目录都可以随意切换

**Linux相对：**cd x1/x2        在当前目录中寻找子目录x1, 在x1中寻找子目录x2

​                         必须确定当前目录下有子目录x1



**pwd**(print working directory) 功能：显示当前的所在目录。返回绝对路径





**mkdir（make directoriy）功能： 创建目录**

**mkdir -p** **文件夹名称** 

​	-p 父目录不存在情况下先生成父目录 （parents）

需要在/root/t1目录下创建一个t2目录

位置：/root

命令：相对：mkdir t1/t2

​          绝对：mkdir /root/t1/t2

需要在/root/t3目录下创建一个t4目录

位置：/root

条件：t3和t4都不存在

命令：mkdir -p t3/t4



### 文件浏览命令

日志文件,XML,properties文件



**cat** **文件名**   快捷查看当前文件的内容

​                         cat适合查看少量信息的文件。



**more 文件名   分页显示文件内容**

1. 操作

​        **Enter**    向下n行，需要定义。默认为1行

​        **空格键**  向下滚动一屏 或 Ctrl+F

​        B  返回上一屏 或 Ctrl+B

​        **q**       退出more



**less -mN** **文件名**     分页显示文件内容，标注每行的行号

1. 操作

    **Enter**    向下n行，需要定义。默认为1行

    **空格键**  向下滚动一屏 或 Ctrl+F

    B  返回上一屏 或 Ctrl+B

    **q**       退出more

    适用于大量数据的查看



**tail -数字 文件名    可以快速查看文件后X行的内容**

例如：tail -10 文件名  查看文件的后10行内容

与之相对应的为head



### 文件操作命令



**Alt  + p进入上传界面**



**文件复制：**

**(copy)--àcp复制文件或者复制目录**

**复制文件：**

**cp 需要复制的文件 复制的位置**

需求：把/root/Demo1.java文件 复制到  /root/t1 目录中

位置：/root

命令：cp Demo1.java t1

​          cp /root/Demo1.java /root/t1

需求：把/root/Demo1.java文件 复制到  /root/t1 目录中，改名为ddd.java

位置：/root

命令：cp Demo1.java t1/ddd.java

复制目录

cp -r 需要复制的文件 复制的位置

需求：把/root/t5目录 复制到  /root/t1目录中

位置：/root

命令：cp -r t5 t1

​	cp -r /root/t5 /root/t1



**文件移动**

**mv(move) 功能：移动或更名现有的文件或目录。**

文件/目录移动：

mv需要移动的文件 移动的位置

需求：把/root/t5 目录 移动到 /root/t3目录中

位置：/root

命令：mv t5 t3

需求：把/root/t5 目录 移动到 /root/t3目录中

​                 不询问，直接覆盖

位置：/root

已知：/root/t3目录中 已经有一个t5目录了

命令：mv -f t5 t3

文件/目录更名操作：

需求：把/root/Demo1.java  改名为 hehe.java

位置：/root

命令：mv Demo1.java hehe.java



删除文件：

**rm** **文件名**

删除目录：

**rm -rf目录名**

需求：删除/root/t5 目录，

位置：/root

已知：t5目录下有子目录或者文件

命令：rm -rf t5

注意：

​        rm -rf *     删除当前目录下所有内容

​        rm –rf /*    删除Linux系统根目录下所有内容



**查找命令：**

**find功能：查找文件或目录。**

语法：**find** **目录名 -name '****需要查找的字符串'**

表示0~多个任意字符

需求：查找/root目录下 所有以ins开头的文件或者目录

位置：/root

命令：find /root -name ‘ins*’



### 文档编辑命令



Windows是有记事本

Linux也有类似的记事本功能   VIM编辑器

**vim命令**

1. 输入“**vim** **文件名**”进入“一般模式”
2. 按下“i”从一般模式，进入“插入模式”
3. 按下“esc”从“插入模式”退出到“一般模式”
4. 在“一般模式”下，输入“:wq”，退出编辑。

![img](D:\java\md_pictures\系统\Linux记事本.png)

一般模式：可以**浏览文件内容**，可以进行文本快捷操作。  单行复制，多行复制，单行删除，多行删除。。。

​                         按i可以切换到插入模式

​                         按:可以切换到底行模式

插入模式：可以编辑文件内容。

​                         按ESC可以切回一般模式

底行模式：可以进行强制退出不保存操作   **q!**

​                   可以进行保存并退出操作  **wq**



### grep和管道

grep 正则表达式。    字符串搜索工作

格式：**grep -i 需要搜索的字符串 搜索的文件**

-i 忽略大小写查找数据

符合规则，就会返回符合规则的行。

管道命令： 可以链接多个Linux命令

![img](D:\java\md_pictures\系统\linux管道.png)

|    shift+\

命令1 | 命令2 | 命令3……

需求：查询当前目录中，所有带ins关键字的行数据

位置：/root

思考：查询当前目录内容  ll

​          查询字符串，返回字符串所在行  grep

命令：ll | grep ins



### 压缩、解压命令【重要】

**Linux压缩，解压命令。**

Linux压缩包： *.tar     打包，大小不会进行压缩

​			***.tar.gz**   打包并压缩文件大小

**压缩：(参数顺序不变)**

​     	   **tar -zcvf 压缩包名字.tar.gz 需要压缩的内容**

例如：tar -zcvf hehe.tar.gz *   将当前目录下所有内容进行打包压缩，文件名hehe.tar.gz

**解压：(参数顺序不变)**

​        	**tar -zxvf 需要解压的压缩包名称   解压到当前目录**

​       	 **tar -zxvf** **需要解压的压缩包名称 -C** **指定压缩路径**    解压到指定目录中

需求：需要把/root/t5/hehe.tar.gz  压缩包 解压到 指定目录中。指定目录/root/t5/t5

位置：/root/t5

命令：tar -zxvf hehe.tar.gz -C /root/t5/t5

tar -zxvf hehe.tar.gz -C t5



### 系统命令

查看系统进程：**ps -ef**

需求：查看进程中 和 vim相关的进程

命令：ps -ef|grep -i vim

强制杀死某个进程：**kill -9 pid****号**

需求：查看进程 和 vim相关的进程有哪些，并将vim进程杀死

命令：ps -ef|grep -i vim

​          Kill -9 pid号

查看网络配置：ifconfig

测试和目标的连通性：ping ip地址

会持续不断一直测试，直到ctrl+c

Linux centos重启命令：reboot

Linux centos关机命令：halt





