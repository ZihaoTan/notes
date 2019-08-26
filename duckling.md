# Duckling
## 简介
`Duckling`可以对文本进行语法分析，并从中得到结构话的数据。`Duckling`是一个基于Haskell的库。
```
"the first Tuesday of October"
=> {"value":"2017-10-03T00:00:00.000-07:00","grain":"day"}
```

## 安装使用
由于需要Haskell环境，[官方](https://github.com/facebook/duckling)推荐使用[stack](https://haskell-lang.org/get-started)。

对于Mac用户，还另外需要`PCRE`这个工具，可以使用`Homebrew`安装

关于`Homebrew`的安装，可以参考[这里](https://github.com/ZihaoTan/notes/blob/master/use_homebrew.md)

继续回到安装duckling上面，安装好homebrew后，可以使用`brew install`需要的包。
```
brew install stack
brew install pcre
```
安装后，可以使用`brew list`查看已经安装的包
```
cabal-install	haskell-stack	python		sqlite
gdbm		openssl		readline	xz
ghc		pcre		sphinx-doc
```
根据官方指导，使用stack来编译和运行二进制文件。
```
$ stack build
```
一切安装好之后
```
stack exec duckling-example-exe
no port specified, defaulting to port 8000
Listening on http://0.0.0.0:8000
```
此时一个最基础的HTTP服务器已经在后台运行了，然后可以通过`curl -XPOST http://0.0.0.0:8000/parse --data 'locale=en_GB&text=tomorrow at eight'`来进行测试。

结果如下：
```
'tomorrow at eight'
[{"body":"tomorrow at eight","start":0,"value":{"values":[{"value":"2019-08-27T08:00:00.000-07:00","grain":"hour","type":"value"},{"value":"2019-08-27T20:00:00.000-07:00","grain":"hour","type":"value"}],"value":"2019-08-27T08:00:00.000-07:00","grain":"hour","type":"value"},"end":17,"dim":"time","latent":false}]

'42€'
[{"body":"42€","start":0,"value":{"value":42,"type":"value","unit":"EUR"},"end":3,"dim":"amount-of-money","latent":false}]

'1秒钟'
[{"body":"1秒钟","start":0,"value":{"second":1,"value":1,"type":"value","unit":"second","normalized":{"value":1,"unit":"second"}},"end":3,"dim":"duration","latent":false}]
```

### python-duckling

### 安装
目前还有一个基于`duckling`封装的python包

使用`pip install duckling`

安装过程可能会报错，因为需要`Jpype1`模块，可以通过运行`conda install -c conda-forge jpype1`解决。

### 例子
##### High-level (DucklingWrapper)
```python
    d = DucklingWrapper()
    print(d.parse_time(u'Let\'s meet at 11:45am'))
    # [{u'dim': u'time', u'end': 21, u'start': 11, u'value': {u'value': u'2016-10-14T11:45:00.000-07:00', u'others': [u'2016-10-14T11:45:00.000-07:00', u'2016-10-15T11:45:00.000-07:00', u'2016-10-16T11:45:00.000-07:00']}, u'text': u'at 11:45am'}]
    print(d.parse_temperature(u'Let\'s change the temperatur from thirty two celsius to 65 degrees'))
    # [{u'dim': u'temperature', u'end': 65, u'start': 55, u'value': {u'unit': u'degree', u'value': 65.0}, u'text': u'65 degrees'}, {u'dim': u'temperature', u'end': 51, u'start': 33, u'value': {u'unit': u'celsius', u'value': 32.0}, u'text': u'thirty two celsius'}]
```
##### Low-level (Duckling)
```python
    d = Duckling()
    d.load() # always load the model first
    print(d.parse('tomorrow'))
    # [{u'body': u'tomorrow', u'dim': u'time', u'end': 8, u'value': {u'values': [{u'grain': u'day', u'type': u'value', u'value': u'2016-10-10T00:00:00.000-07:00'}], u'grain': u'day', u'type': u'value', u'value': u'2016-10-10T00:00:00.000-07:00'}, u'start': 0}]
```