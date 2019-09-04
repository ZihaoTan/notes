#Rasa UI
Rasa UI提供一个网页界面来管理Rasa中的NLU和CORE部件，除此之外还提供训练、加载模型；检测使用；浏览日志等功能。

## 如何开始使用
### Prerequisites
需要[Node.js/npm](https://nodejs.org/en/)

### 安装
```
git clone https://github.com/paschmann/rasa-ui.git
cd rasaui && npm install
```

还需要安装PostgreSQL。对于Ubuntu 16.04的用户，可以浏览[官方教程](https://github.com/paschmann/rasa-ui/wiki/Rasa-UI-Install-Guide)。

对于Mac用户，可以使用`homebrew`安装，或者在官网下载。

#### 使用homebrew安装
1. brew安装
```
brew install postgresql
```

2. 初始化
```
initdb /usr/local/var/postgres
```

3. 启动服务
```
pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
```
停止服务是
```
pg_ctl -D /usr/local/var/postgres stop -s -m fast
```

4. 登陆控制台
```
psql
```
出现了错误如下
```
psql: could not connect to server: No such file or directory
	Is the server running locally and accepting
	connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
```
暂时没有找到相应的解决方法，所以在官网下载PostgreSQL。

#### 通过官网安装
```
Quick Installation Guide
1.Download
2.Move to /Applications
3.Double Click

Done! You now have a PostgreSQL server running on your Mac. 
To use the command line programs, set up your $PATH. 
If you prefer a graphical app, check out the list of GUI tools.

If you get an error saying “the identity of the developer cannot be confirmed”, 
please make sure you didn’t skip step 2. (more info)
```
由于之前用`homebrew`安装过psql，所以会提示`5432`端口别占用，因此我改成了`5433`。

使用`psql -p 5433 -h localhost`发现可以登陆psql控制台。

按照rasa-ui官方的指引，下载psql的Schema并创建对象。
```
wget https://raw.githubusercontent.com/paschmann/rasa-ui/master/resources/dbcreate.sql
```

发生报错，具体如下：
```
wget https://raw.githubusercontent.com/paschmann/rasa-ui/master/resources/dbcreate.sql
--2019-09-04 09:50:53--  https://raw.githubusercontent.com/paschmann/rasa-ui/master/resources/dbcreate.sql
正在解析主机 raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.108.133
正在连接 raw.githubusercontent.com (raw.githubusercontent.com)|151.101.108.133|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 404 Not Found
2019-09-04 09:50:54 错误 404：Not Found。
```
在翻墙状态下用浏览器打开网页也是出现404错误

#### 使用homebrew安装
1. brew安装
```
brew install postgresql
```

### 运行
在rasa-ui文件夹使用npm start来启动服务器
```
npm start
```
做完这一步之后在[http://localhost:5001](http://localhost:5001)可以查看到。

因为PostgreSQL还没配置好的原因，暂时无法使用里面的如上传数据、训练NLU等功能。