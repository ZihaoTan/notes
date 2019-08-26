# 关于Homebrew
由于涉及到`Homebrew`，这里也简单记录下使用过程中碰到到问题。目前国内的使用环境，本来一些简单的安装也变得格外困难。
电脑之前也安装过`Homebrew`，但可能由于用户名改了或者是一些环境变量的问题。导致`brew update`的时候一直没反应，使用`brew doctor`还出现了无权限写入`/usr/local`。网上查阅了一下资料，可能是由于Mac OS权限机制的改动，作些修改就可以。但有一个更简单的解决方法就是重装`Homebrew`。

## 卸载
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

## 安装
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