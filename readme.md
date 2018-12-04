# 项目简介    

   这是用springboot做的天猫整站。模仿天猫整站分前端和后端两部分。

  前端页面分为：首页，分类页，查询结果页，产品页，结算页面，支付页面，支付成功页面，购物车页面，我的订单页面，确认收货页面，确认收货成功页面，进行评价页面，登录页面，注册页面。一共14种页面。

  后端分为7个步骤：分类管理，分类属性管理，产品管理，产品属性设置，产品图片管理，用户管理，订单管理。

## 本地演示   

### 步骤1：下载并运行

​	为了证明这是个可以运行的项目，提供了下载一个300多m的压缩包。解压之后如图：  

![项目结构](https://github.com/sakurayura/tmall-sb/blob/master/images/tianmaoxiangmstructure.png)

​	模仿天猫整站 springboot版本地演示启动思路：

```
1. 因为springboot 版用到了 redis 和 elasticsearch，所以启动过程，请务必按照顺序启动
2. 不同软件之间存在兼容问题，如果您本机已经装有了 redis或者 elasticsearch 或者 kibana, 但是版    本不对，请暂停本机版本，使用我提供的版本
3. 启动顺序请按照批处理前的序号，依次启动：
   3.1 1-redis.bat
   3.2 2-elasticsearch.bat
   3.3 3.kibana.bat
   3.4 4-tomcat.bat
```

### 步骤2：访问地址

​	考虑到同学的机器上已经有tomcat，可能已经占用了80,8080端口，所以这个Tomcat是用了9090端口，避免和其他端口冲突。

​	请访问如下地址。 前台演示：

```
http://127.0.0.1:9090/tmall_springboot/home
```

​	后台管理：

```
http://127.0.0.1:9090/tmall_springboot/admin_category_list
```

这是别人部署在其它服务器上的演示：

```
http://how2j.cn/tmall
http://how2j.cn/tmall/admin
```

​	用户感受上一样，但是在技术上是不一样的，其中一个重要改动是前后端都做了前后端分离，数据都是通过 axios.js 封装 ajax 到服务端取的数据，然后再由 vue.js 把数据展示开。

### 步骤3：nginx

​	接下来进行 nginx 的配置和启动。 为什么不像其他几个步骤一样搞一个 5-nginx.bat 呢？ 因为 nginx 要做动静分离，必须配置路径才可以使用。 不像其他几个步骤，站长已经做了铺垫，直接点击就可以用了。这里使用 nginx 主要做两件事：

```
1. nginx 采用80端口，反向代理到上述tomcat的9090端口。 这样用户访问的时候，就 访问80端口
2. 动静分离，对于图片，css,js 等等一系列静态资源，都直接走 nginx， 而不再通过 tomcat了。
```

### 步骤4：nginx.conf 配置文件

打开nginx目录下的 nginx.conf 配置文件。1. 反向代理配置。 这个已经配置好了，无需改动

```
proxy_pass http://127.0.0.1:9090;
```

2. 动静分离， 这个动静分离我提供的路径是：

   ```
   location ~\.(css|js|png|ttf|woff|woff2|eot|svg|map|jpg|gif)$ {
       root E:/project/tomcat_with_jdk_hsqldb_springboot/tomcat-8.5.23/webapps;
   }
   ```

这个路径，每个学习的同学都不一样，所以要根据自己情况，把它修改为自己的路径，一定要是 tomcat的 webapp目录。

### 步骤5：启动nginx

启动方式： 从控制台切换到 nginx 所在目录：

```
e:
E:\project\tomcat_with_jdk_hsqldb_springboot\nginx
```

启动命令：

```
start nginx
```

### 步骤6：访问测试

访问地址使用80，也就无需显示指定端口号了：
前台演示：

```
http://127.0.0.1/tmall_springboot/home
```

后台管理：

```
http://127.0.0.1/tmall_springboot/admin_category_list
```

### 步骤7：动静分离测试

比如访问地址：

```
http://127.0.0.1/tmall_springboot/img/category/83.jpg
```

可以看到图片，但是并不能确定，这个图片到底是 nginx 直接访问到的，还是tomcat 那里来的。
这个时候，关闭9090端口的 tomcat, 再访问这个地址，发现依然可以访问，说明静态资源都是从 nginx 这里访问的了。当然，关闭 9090 端口的tomcat之后，也就只有静态资源可以访问了，其他的业务数据就没法用了。

## 开发环境演示

上面是仅仅是为了演示效果，这里搭建起一个实际的开发环境，在开发环境下实际运行这个项目，以明确该项目是可以在开发环境下运行起来的。

首先下载tmall_springboot.sql文件(用来导入数据)和其他一些环境等。这里使用JDK1.8和mysql5.5版本。注意不要用 navicat,mysql-front 等工具导入，因为数据量大，这些工具处理不了，会报奇奇怪怪的错误。

本tmall_springboot.sql 包含17个分类，每种分类下5个产品，总计85件产品。 每个产品下有20余张图片，总计1777张图片，以及属性和属性值等信息。

在导入数据之前，需要mysql服务器上存在tmall_springboot这个数据库：

```
CREATE DATABASE tmall_springboot DEFAULT CHARACTER SET utf8;
```

然后在当前 mysql 环境下输入 exit 退出 mysql 环境，才能进行下面的在cmd环境下的导入
考虑到要导入的tmall_springboot.sql文件比较大，通过工具如mysql-front导入的时候，可能会出错导致失败，建议使用命令行导入：

```
"C:\Program Files\MySQL\MySQL Server 5.5\bin\mysql.exe" -u root -proot --default-character-set=utf8 tmall_springboot < d:\tmall_springboot.sql
```

其中：帐号和密码都是root。C:\Program Files\MySQL\MySQL Server 5.5\bin\mysql.exe 这个路径是我的mysql.exe的路径。<后面是sql文件的位置。
