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

### 关于Homebrew
由于涉及到`Homebrew`，这里也简单记录下使用过程中碰到到问题。目前国内的使用环境，本来一些简单的安装也变得格外困难。
电脑之前也安装过`Homebrew`，但可能由于用户名改了或者是一些环境变量的问题。导致`brew update`的时候一直没反应，使用`brew doctor`还出现了无权限写入`/usr/local`。网上查阅了一下资料，可能是由于Mac OS权限机制的改动，作些修改就可以。但有一个更简单的解决方法就是重装`Homebrew`。

#### 卸载
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

#### 安装
如果可以通过运行下面的代码完成安装，请跳过安装的步骤。
```
curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install
```

由于官方安装会出现连不上的问题，因此我们需要把安装指令修改一下，运行
```
curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install >> brew_instal
```
会在终端所在目录下生成一个brew_install的脚本，对它进行编辑，把`BREW_REPO`和`CORE_TAP_REPO`（若存在）修改为国内清华镜像源。
```
BREW_REPO = "https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git".freeze
CORE_TAP_REPO = "https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git".freeze
```  

运行`brew_install`
```
/usr/bin/ruby ./brew_install
```
如果出现下面对代码，可以不用等待，直接进行下一步操作。
```
==> Tapping homebrew/core
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core'...
```
把`homebrew repo`切换为清华镜像
```
cd "$(brew --repo)"

git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"

git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git
```
如果提示不存在`homebrew-core`目录，可以`mkdir`直接创建。

最后使用`brew update`应该可以显示homebrew已经成功安装。

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
[{"body":"tomorrow at eight","start":0,"value":{"values":[{"value":"2019-08-27T08:00:00.000-07:00","grain":"hour","type":"value"},{"value":"2019-08-27T20:00:00.000-07:00","grain":"hour","type":"value"}],"value":"2019-08-27T08:00:00.000-07:00","grain":curl -XPOST http://0.0.0.0:8000/parse --data 'locale=en_GB&text=42€'
[{"body":"42€","start":0,"value":{"value":42,"type":"value","unit":"EUR"},"end":3,"dim":"amount-of-money","latent":false}]tan:~ tan$ curl -XPOST http://0.0.0.0:'000/parse --data 'locale=zh_CN&text=1秒钟'
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