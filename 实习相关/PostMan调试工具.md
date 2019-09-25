# Postman 使用方法详解

## **Postman背景介绍**

​	用户在开发或者调试网络程序或者是网页B/S模式的程序的时候是需要一些方法来跟踪网页请求的，用户可以使用一些网络的监视工具比如著名的Firebug等网页调试工具。Postman不仅可以调试简单的css、html、脚本等简单的网页基本信息，它还可以发送几乎所有类型的HTTP请求！Postman在发送网络HTTP请求方面可以说是Chrome插件类产品中的代表产品之一。

## **Postman的操作环境**

​	postman适用于不同的操作系统，Postman Mac、Windows X32、Windows X64、Linux系统，还支持postman 浏览器扩展程序、postman chrome应用程序等。

​	Postman使用参考文档：

​	1.[官方英文文档](https://www.getpostman.com/docs/v6/)

## **Postman下载安装**

​	Postman官网下载地址：

​	<https://www.getpostman.com/apps>

## **Postman的基础功能**

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_1.png)

## **接口请求流程**

``` 
1. GET 请求
GET请求：点击Params，输入参数及value，可输入多个，即时显示在URL链接上，所以，GET请求的请求头与请求参数如在接口文档中无特别声明时，可以不填。
```

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_1.png)

响应示例： 这里会有请求的响应状态码，响应时间，以及响应大小

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_2.png)

响应体示例： 响应的格式可以有多种，这里由于请求的是 百度，因此响应的是 html ,一般情况下，自定义接口的话是 json格式的响应体

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_get_3.png)



``` java
2. POST请求
POST请求一：表单提交
```

下图示例中设置了请求方法，请求URL，请求参数，但是没有设置请求头在我的使用过程中，请求头是根据请求参数的形式自动生成的请求头中的Content-Type与请求参数的格式之间是有关联关系，比如：

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180524000345232?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_3.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_4.png)

POST请求二：json提交
下图中，当我们选择JSON(application/json) 是会自动帮我们设置 headers 为 application/json
![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_5.png)

POST请求三：xml提交

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_6.png)

POST请求四：二进制文件提交

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_post_7.png)

其它请求方式如PUT,DELETE 大致流程和GET,POST 差不多，这里就不一一举例说明了

## **管理用例—Collections**

``` 
在POST基础功能那里有一张图片大致说了一下Collections 的作用， 这里我们再详细说明一下
```

Collections集合：也就是将多个接口请求可以放在一起，并管理起来。什么样的接口请求可以放在同一个collection里？

比较方便的方法是，一个工程一个Collection，这样方便查找及统一处理数据。

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_1.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_2.png)

第一步, 创建Collections
点击上图中的带+号的图标，输入Name:”abc”，Description:”示例demo”，点击Create按钮即创建成功一个Collections.

第二步，在Collections里添加请求

在右侧准备好接口请求的所有数据，并验证后，点击save按钮。

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_3.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_4.png)

collections 管理精细化， 这里我们针对不同的请求方式做分组——添加子文件夹

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_5.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_6.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\Postman\Postman_Collections_7.png)

## **身份验证Authentication**

1、Basic Auth

是基础的验证，所以会比较简单
会直接把用户名、密码的信息放在请求的 Header 中

2、Digest Auth

要比Basic Auth复杂的多。使用当前填写的值生成authorization header。所以在生成header之前要确保设置的正确性。如果当前的header已经存在，postman会移除之前的header。

3、OAuth 1.0

postman的OAuth helper让你签署支持OAuth

1.0基于身份验证的请求。OAuth不用获取access token,你需要去API提供者获取的。OAuth 1.0可以在header或者查询参数中设置value。

4、OAuth 2.0

postman支持获得OAuth 2.0 token并添加到requests中。