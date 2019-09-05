# Rasa UI
Rasa UI提供一个网页界面来管理Rasa中的NLU和CORE部件，除此之外还提供训练、加载模型；检测使用；浏览日志等功能。

## 如何开始使用
### Prerequisites
需要[Node.js/npm](https://nodejs.org/en/)

### 安装
```
git clone https://github.com/paschmann/rasa-ui.git
cd rasaui && npm install
```

~~还需要安装PostgreSQL。对于Ubuntu 16.04的用户，可以浏览[官方教程](https://github.com/paschmann/rasa-ui/wiki/Rasa-UI-Install-Guide)。具体安装过程可以参考[这里](https://github.com/ZihaoTan/notes/blob/master/psql.md)~~

跟Rasa-UI的开发者沟通过，最新版本已经不使用PostgreSQL，转而使用Sqlite。在安装Rasa的过程中，SQLite会自动安装，problem solved!😄😄

### 运行
由于Rasa-UI是基于Rasa，因此需要安装Rasa并启动Rasa服务器。具体惨做可以参考[官网指引(I want to learn how to use Rasa 🚀](https://rasa.com/docs/rasa/user-guide/rasa-tutorial/)

然后在Rasa项目里运行：
```
rasa run --enable-api
```
`--enable-api`非常重要，只有这样才能开启HTTP服务器。
```
root  - Starting Rasa server on http://localhost:5005
```
可以看到已经在5005端口上运行。

然后把rasa-ui文件夹下`package.json`中`rasa_endpoint`修改为上面的端口。
`"rasa_endpoint": "http://localhost:5005"`

在rasa-ui文件夹使用npm start来启动服务器
```
npm start
```
做完这一步之后在[http://localhost:5001](http://localhost:5001)可以查看到。

运行的界面如下：
![Training](https://github.com/ZihaoTan/notes/blob/master/img/UI-Training.png)

# Rasa X
## 如何开始使用
### 安装
运行`pip install rasa-x --extra-index-url https://pypi.rasa.com/simple`就可以了

### 运行
只需要在Rasa项目里运行：
```
rasa x
```