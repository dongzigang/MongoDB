<h2>windows下安装mongodb</h2>
1.去官网下载最新版本（以安装在E盘为例）在windows自带的CMD中安装

下载服务器版即可

2.在E盘新建文件夹mongodb

3.将下载的安装包放到mongodb文件夹中，点击安装，

  安装过程中，你可以通过点击 Custom——Browse> 按钮来设置安装目录为当前文件夹
  
4.安装好之后在mongodb目录下新建如下文件夹及文件

  data文件夹——>db文件夹
  
  logs文件夹——>mango.log文件
  
  mongodb.config文件
  
  mongodb.config文件内容如下
  ```
  ##数据文件
  dbpath=E:\mongodb\data\db
  ##日志文件
  logpath=E:\mongodb\logs\mongo.log
  ##错误日志采用追加模式，配置这个选项后mongodb的日志会追加到现有的日志文件，而不是从新创建一个新文件
  logappend=true 
  #启用日志文件，默认启用
  journal=true 
  #这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
  quiet=true 
  #端口号 默认为27017
  port=27017 
  ```
 5.以管理员身份运行cmd（win+x再按A）
 
 输入如下命令
 
 进入E盘
 ```
 E:
 ```
 ```
 cd E:\mongodb\bin
 ```
 ```
 mongod.exe --dbpath E:\mongodb\data\db
 ```
 如果出现一大段日期则表示连接成功

mongodb默认连接端口27017，可以打开http://localhost:27017查看

6.将mongodb设置为windows服务

用管理员身份打开cmd命令行，进入E:\mongodb\bin目录，输入如下的命令：
```
mongod --config E:\mongodb\mongo.config --install --serviceName "MongoDB"
```
7.启动

打开cmd输入services.msc查看服务可以看到MongoDB服务，点击可以启动。
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
