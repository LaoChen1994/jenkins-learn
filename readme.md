# jenkins简单学习笔记

## 0. 参考文档

[前端自动打包](https://www.cnblogs.com/tugenhua0707/p/11949644.html)

[ngrok官网](https://www.ngrok.cc/_book/start/ngrok_linux.html)

## 1. 学习目标

> 没有目标的学习 = 瞎学

通过jenkins的学习最终能完成通过代码的自动化构建～这就是我们的目标啦～

## 2. 环境的搭建

### 2.1 java环境的搭建

链接：[java 8官网下载链接](https://www.java.com/zh-CN/download/help/mac_install.html)

点击对应的版本进行下载，之后点击安装即可

安装完成后，验证如下, 说明java已经安装成功：

```bash
java -version

// java version "1.8.0_291"
// Java(TM) SE Runtime Environment (build 1.8.0_291-b10)
// Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)
```

### 2.2 安装jenkins

链接：[jenkins链接](http://updates.jenkins-ci.org/download/war/)

1. 下载的是war包，当然[官网文档](https://www.jenkins.io/zh/doc/book/installing/)中也给了很多方法，例如容器啊，brew安装等，这里我们采用war包

2. 终端通过命令打开

   ```java
   java -jar jenkins.war
   ```

3. 浏览http://localhost:8080并等到*Unlock Jenkins*页面出现,说明环境已经搭建完毕

   ![image-20210703202939458](./images/image-20210703202939458.png)

## 3. 按照引导完成jenkins的启动

1. 密码登陆：到对应的密码文件夹下找到初始密码

2. 安装插件：先选择安装默认的插件即可

   ![image-20210703204638158](./images/image-20210703204638158.png)

3. 创建用户

   ![image-20210703205251370](./images/image-20210703205251370.png)

4. 一路按照对应的提示点击，即可进入jenkins的主页面

   ![image-20210703205345336](./images/image-20210703205345336.png)

## 4. 安装nodejs插件

1. 进入`插件配置`，来增加NodeJS的插件,当展示为success的时候说明添加成功

   ![image-20210703210034908](./images/image-20210703210034908.png)

2. 进入`全局系统配置`，点击`新增NodeJS`安装对应的nodejs版本, 添加完安装信息后，点击`保存`即可

   ![image-20210703210204785](./images/image-20210703210204785.png)![image-20210703210251062](./images/image-20210703210251062.png)

## 5. 配置NodeJS的自动部署任务

### 5.1 配置新任务

首页点击`新建任务`，选择自由风格的任务即可，点击`确定`

![image-20210703210700899](./images/image-20210703210700899.png)

### 5.2 增加新的build配置

1. 点击刚才创建的项目，然后点击`配置`

   ![image-20210703215045797](./images/image-20210703215045797.png)

2. 设置对应的git去拉代码的地址

![image-20210703215425826](./images/image-20210703215425826.png)

3. 配置对应的node运行环境

   ![image-20210703215505949](./images/image-20210703215505949.png)

4. 设置打包的命令

   ![image-20210703215559849](./images/image-20210703215559849.png)

   **注**：这里的dep:install是项目中script里面配的一个安装依赖的命令

### 5.3 build

1. 触发build

![image-20210703215726066](./images/image-20210703215726066.png)

2. 点击`build now`来开始第一次构建，对应的构建结果可以点击这个时间看到，点击`控制台`可以看到构建的记录

![image-20210703215819221](./images/image-20210703215819221.png)

## 6. 自动化构建

目前是我们手动触发这个build的，我们希望在代码push的时候自动build，这种时候我们可以通过webhook的方式实现。

### 6.1 申请仓库对应的access token

> 申请路径：https://github.com/settings/tokens
>
> 从目录进的话就是：头像 -> setting -> Developer settings -> Personal access token -> generate new token

### 6.2 对该token进行授权配置，选中对应的repo hooks

![image-20210703221010877](./images/image-20210703221010877.png)

### 6.3 配置token

在jenkins中添加token，`配置` -> `系统配置` -> `找到github` -> `添加github server` -> 添加刚才的token凭据

**注**：如果这里的github server找不到可以在插件市场中安装`Github plugin`

![image-20210703221704269](./images/image-20210703221704269.png)

### 6.4 测试连接

点击测试，看看token是否生效

![image-20210703222018669](./images/image-20210703222018669.png)

### 6.5 利用ngrok做内网穿透

什么是**内网穿透**？

**简单讲：**将本地的127.0.0.1:xxx代理到公网上端口上暴露给外部可访问

#### 6.5.1 申请ngrok的免费隧道

![image-20210704114723696](./images/image-20210704114723696.png)

#### 6.5.2 填写需要代理的端口信息

因为我们jenkins本地的端口是8080，所以需要代理到外部的端口是8080

![image-20210704114832903](./images/image-20210704114832903.png)

#### 6.5.3 下载相应的客户端

下载系统对应的客户端，然后到本地使用就可以

![image-20210704115130057](./images/image-20210704115130057.png)

#### 6.5.4 运行客户端

下载下来的客户端，mac就是一个unix可执行文件，可能运行的时候会报不安全的错误，解决办法：[mac不安全解决办法](https://jingyan.baidu.com/article/1612d5008ea6b8e20e1eee9b.html)，然后使用

```bash
# 运行sunny内网穿透
sunny clientid xxxxx
```

打开对应的赠送域名即可，现在会通过域名直接访问到对应的jenkins页面

**注意：**这里切换完代理的域名之后会出现 *反向代理有误* 的问题，解决办法: [解决jenkins反向代理有误的问题](https://blog.csdn.net/qq_38161040/article/details/100900558)

![image-20210704113204622](./images/image-20210704113204622.png)

### 7. 全流程的联调

#### 7.1 配置webhook

进入我们仓库的github，然后选择对应项目的`setting` -> `webhooks` -> `add hooks`进入添加hooks，这里将webhooks触发事件后的url地址写到我们反向代理出来的url地址上，**注意：这里jenkins处理web hook的地址是/github-webhook/**,这里一定要加上，如果不加上的话，会出现github webhook请求失败的情况

![image-20210704122646788](./images/image-20210704122646788.png)

#### 7.2 开启webhook 轮询

![image-20210704123316729](./images/image-20210704123316729.png)

#### 7.3 推一下代码之后就可以自动构建拉

推完代码之后，webhook推到了对应的jenkins的github-webhook的接口上，然后就自动触发了我们的build的代码，并且可以看到是通过github的webhook进行触发

![image-20210704123432778](./images/image-20210704123432778.png)

![image-20210704123619547](./images/image-20210704123619547.png)