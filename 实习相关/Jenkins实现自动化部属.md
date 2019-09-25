# 搭建jenkins实现自动化部署

Jenkins是一个非常有名的CI工具，开源、免费，通过jenkins我们可以更加智能、快速的持续集成，尽早的发现代码里的问题并及时的部署上去。

### Install Jenkins On Ubuntu

``` shell
wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'sudo apt-get update
sudo apt-get install jenkins
```

打开[http://localhost:8080](http://localhost:8080/)如果看到Jenkins的首页说明你已经安装成功了。

### 更改端口

如果端口8080已经被占用，要修改配置文件来改变端口。

```shell
sudo vi /etc/default/jenkins
```

找到`HTTP_PORT=8080`，把8080改为8000，然后

```shell
sudo service jenkins restart
```

### 配置访问权限

Jenkins默认无需登录即拥有所有权限，这肯定是极不安全的。这里简单的配置一下，让用户登录后才可以操作。

配置后的最终结果截图：

![img](D:\java\md_pictures\Git\Jenkins配置.png)

​	在Jenkins左侧栏里点击“系统管理”，然后“Configure Global Security”，勾上“启用安全”。在安全域里选择“Jenkins专有用户数据库”，并勾上“允许用户注册”。保存后进入首页注册一个账号，譬如我们这里注册一个叫jenkins的用户。然后回到刚才的配置页面，在“授权管理”里选择“项目矩阵授权策略”，将jenkins用户添加到用户组，并在jenkins那一行勾上“Administrator”，取消“匿名用户”的所有授权。最后，取消允许用户注册，点击保存。这样一来，就只能用jenkins账号登陆才能做后续操作。

### 新建任务

​	在左侧栏选择新建，填上名称，选择构建一个自由风格的软件项目，点击保存，进入到配置页。这个页面涵盖了对这个任务的所有配置，有些复杂，下面会一一讲解。

![img](D:\java\md_pictures\Jenkins\新建任务.png)

#### 源码管理

​	选择Git, Repository URL里填上Github的地址，Credentials -> Add，填上Github的用户名密码。这个是Jenkins拉取源码时需要的凭证。 "Branches to build"填上分支名，表示Jenkins将拉取该分支的源码，后面触发器也只针对该分支。

![img](D:\java\md_pictures\Jenkins\源码管理.png)

#### 构建触发器

​	表示在什么情况下构建项目，如果选择“Poll SCM”，然后在日程表里填上cron表达式，例如"H/5 * * * *"，表示每5分钟检查一次，有代码变更就构建项目。这里我们不选Poll SCM，而是用Gitub插件来做到实时构建。

#### Github插件

​	“系统管理” -> "插件管理" -> "可选插件"，搜索“Github plugin”，勾中，直接安装。进入到插件安装页面，待安装成功后，再次进入项目配置页面。这时“构建触发器”里应该多了一项“Build when a change is pushed to GitHub”，选中。接下来要去Github上配置hook。

![img](D:\java\md_pictures\Jenkins\安装GitHub插件.png)



​	登陆Github,在项目的settings -> Webhooks & Services -> Add Hook 里填上 http://jenkins_url/github-webhook/, 记住Content type一定要选择“application/x-www-form-urlencoded”，否则是没办法正常工作的，secret不用填。

![img](D:\java\md_pictures\Jenkins\安装GitHub插件2.png)

#### 构建

在“构建”配置里，选择“增加构建步骤”，选择“Execute shell”，即可填写你要执行的构建命令。对于Java项目，这里一般是Ant或Maven命令。对于我们项目，主要是执行shell命令。

![img](D:\java\md_pictures\Jenkins\构建.png)

依次增加如下构建命令：

``` shell
npm install	
grunt deploy
cd public/assets/javascript
gruntbin/migrate_database.sh
NODE_ENV=test bin/migrate_database.sh
```

以上按顺序分别是安装依赖包，编译coffee，更新数据库和model字段。再加上最后一步——测试：

``` shell
NODE_ENV=test mocha --compilers coffee:coffee-script/register test/spec/**/*.coffee -R doc > test_result.html
```

​	这些命令可以放在一个里面，也可以分开放，但是顺序一定要保持如此。尤其是最后一步。如果测试通过，构建结果就是成功；相反，构建会被标记为失败。这决定了后面要说的构建后操作。

​	完成以上步骤后，已经实现了任务的自动构建，但是为了让脚本能够跑起来，还需要最后一步，即给jenkins用户配置开发环境。

​	当`apt-get install jenkins`后，系统就多了一个jenkins用户，其主目录在/var/lib/jenkins，jenkins也是以该用户的身份在运行。所以在这个环境下，你当前登录的用户所配置的诸如npm grunt是无法在jenkins里正常使用的。所以必须先`su jenkins`配置环境，在这之前可能你先要`sudo passwd jenkins`来给jenkins设置一个密码，并将jenkins用户加到sudo列表以安装npm。

​	做完以上配置之后，可以点击“立即构建”测试一下构建过程是否正确。然后往github推送一次，看是否会触发jenkins进行构建。确保以上都没有问题后，做接下来的配置。

#### 构建后操作

构建后操作分两种情况：

- 构建成功后，将程序部署上去；
- 构建失败后，发送通知邮件。

但是无论是部署程序还是发送邮件，在“增加构建后操作”里都找不到对应的选项。所以还需要安装一些额外的插件。

按照上面所说的安装插件的方法，安装以下两个插件：

- Post build task
- PostBuildScript

这两个插件都是用来执行shell脚本的，不同的是Post build task插件可以根据不同的构建输出做不同的动作，当前面构建失败时，可以通过匹配失败的输出来执行发送邮件操作。

构建后操作 -> 增加构建后操作步骤 -> Post build task，在Log text里填上marked build as failure，这是构建脚本执行不成功时日志会输出的一段文本。然后在Script里填上

``` shell
coffee ~/mailer/mailer.coffee -f 石墨通知 -t chenxu@1heart.cn,a@1heart.cn -s "Jenkins Build Fail $BUILD_DISPLAY_NAME" -c $BUILD_URL -a test_result.html
```

这一段就是发送email的脚本，`-a test_result.html`是让邮件带上测试结果作为附件。mailer.coffee是我用NodeJS写的发送邮件的脚本。具体参数如下：

``` shell
coffee ~/mailer/mailer.coffee -h
 
Usage: mailer.coffee [options]
 
Options:
 
-h, --help               output usage information-V, --version            output the version number-f, --from [value]       发件人名称-a, --attachment <file>  附件路径-t, --to [value]         收件人邮件地址, 如果有多个以,隔开-s, --subject [value]    邮件主题-c, --content [value]    邮件内容
```

mailer.coffee因为涉及到邮箱账号密码，暂时放在Github私人仓库里。

#### 构建成功后自动部署

增加构建后操作步骤 -> Execute a set of scripts，在Command里填上部署脚本，并勾上Execute script only if build success。这样就实现了构建成功自动部署。以下是部署脚本：

``` shell
# 备份用户上传的文件if [ -d "/var/lib/jenkins/shimo/public/uploads" ]; then
    cp -r /var/lib/jenkins/shimo/public/uploads/ /var/lib/jenkins/backups/fi# 删除原本部署在pm2的程序rm -rf /var/lib/jenkins/shimo/# 将jenkins工作空间下构建好的版本复制出去cp -r /var/lib/jenkins/jobs/shimo/workspace/ /var/lib/jenkins/shimo/# 还原上传文件cp -r /var/lib/jenkins/backups/ /var/lib/jenkins/shimo/public/uploads/# 建立软链接rm /var/lib/jenkins/shimo/node_modules/shimo
ln -s /var/lib/jenkins/shimo/ /var/lib/jenkins/shimo/node_modules/shimo# 部署到pm2pm2 startOrRestart processes.json
```

​	`/var/lib/jenkins/jobs/shimo/workspace/`是jenkins自动构建的位置，这个位置就是项目根目录，但是并不能直接把这个位置部署到pm2。原因是如果构建失败，即使没有重启pm2，前端的html、js都已经更新并生效了。为了防止这个问题，每次构建成功后将项目拷贝到另一个路径，在该路径下部署。

​	完成以上配置后，就实现了项目的CI/CD。Jenkins非常灵活，以上步骤需要根据自己的实际情况做调整。

