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
根据官方指导，使用stack来编译和运行二进制文件
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

`Duckling`支持中文，只需要把`locale`改为`zh_CN`即可。其他语言可以查看[ISO-639-1 standard](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)。

支持的dimension如下：  
| Dimension | Example input | Example value output
| --------- | ------------- | --------------------
| `AmountOfMoney` | "42€" | `{"value":42,"type":"value","unit":"EUR"}`
| `CreditCardNumber` | "4111-1111-1111-1111" | `{"value":"4111111111111111","issuer":"visa"}`
| `Distance` | "6 miles" | `{"value":6,"type":"value","unit":"mile"}`
| `Duration` | "3 mins" | `{"value":3,"minute":3,"unit":"minute","normalized":{"value":180,"unit":"second"}}`
| `Email` | "duckling-team@fb.com" | `{"value":"duckling-team@fb.com"}`
| `Numeral` | "eighty eight" | `{"value":88,"type":"value"}`
| `Ordinal` | "33rd" | `{"value":33,"type":"value"}`
| `PhoneNumber` | "+1 (650) 123-4567" | `{"value":"(+1) 6501234567"}`
| `Quantity` | "3 cups of sugar" | `{"value":3,"type":"value","product":"sugar","unit":"cup"}`
| `Temperature` | "80F" | `{"value":80,"type":"value","unit":"fahrenheit"}`
| `Time` | "today at 9am" | `{"values":[{"value":"2016-12-14T09:00:00.000-08:00","grain":"hour","type":"value"}],"value":"2016-12-14T09:00:00.000-08:00","grain":"hour","type":"value"}`
| `Url` | "https://api.wit.ai/message?q=hi" | `{"value":"https://api.wit.ai/message?q=hi","domain":"api.wit.ai"}`
| `Volume` | "4 gallons" | `{"value":4,"type":"value","unit":"gallon"}`

简单调用服务，结果如下：
```
'tomorrow at eight'
[{"body":"tomorrow at eight","start":0,"value":{"values":[{"value":"2019-08-27T08:00:00.000-07:00","grain":"hour","type":"value"},{"value":"2019-08-27T20:00:00.000-07:00","grain":"hour","type":"value"}],"value":"2019-08-27T08:00:00.000-07:00","grain":"hour","type":"value"},"end":17,"dim":"time","latent":false}]

'42€'
[{"body":"42€","start":0,"value":{"value":42,"type":"value","unit":"EUR"},"end":3,"dim":"amount-of-money","latent":false}]

'1秒钟'
[{"body":"1秒钟","start":0,"value":{"second":1,"value":1,"type":"value","unit":"second","normalized":{"value":1,"unit":"second"}},"end":3,"dim":"duration","latent":false}]

'电话号码是1388882006'
[{"body":"1388882006","start":5,"value":{"value":"1388882006"},"end":15,"dim":"phone-number","latent":false},{"body":"1388882006","start":5,"value":{"value":1388882006,"type":"value"},"end":15,"dim":"number","latent":false}]

'电话号码是+86 (020) 1234-45678'
[{"body":"86","start":6,"value":{"value":"86"},"end":8,"dim":"phone-number","latent":false},{"body":"86","start":6,"value":{"value":86,"type":"value"},"end":8,"dim":"number","latent":false},{"body":"(020) 1234-45678","start":9,"value":{"value":"020123445678"},"end":25,"dim":"phone-number","latent":false}]
```
可见对于已经定义好的dimension能识别的比较好，但对于格式要求比较严格，如手机号识别成number，对于标准的座机号码能识别出为phone-number

## 原理
`Duckling`项目缺少相关论文的介绍，对于其具体实现原理，官网也没有作任何介绍。不过根据它需要安装`PCRE`(Perl Compatible Regular Expressions)来推断，应该主要是利用正则来实现的。

## python-duckling

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