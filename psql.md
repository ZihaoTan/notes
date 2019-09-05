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
由于之前用`homebrew`安装过psql，所以会提示`5432`端口会被占用，因此我改成了`5433`。

使用`psql -p 5433 -h localhost`发现可以登陆psql控制台。
