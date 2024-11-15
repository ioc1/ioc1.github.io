+++
title = "git的换行符问题"
date = 2024-11-12T00:00:00+08:00
lastmod = 2024-11-12T23:26:10+08:00
tags = ["Git"]
categories = ["Tool"]
draft = false
toc = true
+++

最近在MS Windows和Linux之间来回切换开发，发现总是碰到换行符问题，在此总结（大篇转载）。

> 1.  CR:表示回车 `\r`
> 2.  LF:表示换行 `\n`
> 3.  CRLF:表示回车换行 `\r\n`


## 换行符在不同的操作系统上的表示 {#换行符在不同的操作系统上的表示}

首先要理解的一点是，对于不同的操作系统，对于换行符的表示是不一样的。也就是说当我们在编辑一个文件，在键盘上按下回车键的时候，对于不同的操作系统保存到文件中的换行符是不一样的。见下表：

>
>
> 敲下回车键，不同的操作系统保存到文件中的值：
>
> 1.  Windows：使用的是CRLF即 `\r\n` ，文件中保存的是 `\r\n`
> 2.  Linux/Unix: 使用的是 `LF` 即 `\n` ，文件中保存的是 `\n`
> 3.  Mac OS: 使用的是CR即 `\r` ，文件中保存的是 `\r`
> 4.  Mac OS X系统：使用的是LF即 `\n` ，文件中保存的是 `\n` （Mac OS X已经改成和Unix/Linx一样使用LF）

**问题：** 既然不同的操作系统，对于换行符使用不同的表示形式，如果一个团队在开发一个共同的项目，如果你使用的是windows系统，而你的小伙伴用的是Mac的话，当你们使用git协同开发软件时，就会出现换行符不统一的问题。


## Git会自动对换行符进行转换 {#git会自动对换行符进行转换}

Git为了解决上面提出的问题，会自动对换行符进行转换。转换的方案有3种：

> 1.  在提交时将CRLF转换为LF，在拉取（检出checkout）时将UNIX换行符（LF）替换成CRLF。（Windows系统推荐使用，我们在windows上安装git的时候，如果一路next，默认是使用这个方案）
> 2.  在提交时将CRLF转换为LF，在拉取（检出checkout）时不进行转换。（Linux/Unix和Mac OS和Mac OS X推荐使用，在Unix或者类Unix操作系统上安装git，默认使用这种方案）
> 3.  不进行转换（这种方案对于跨平台项目不推荐使用）。

可以发现，如果不使用第3种方案，那么在Git仓库（包括本地仓库和GitHub远程仓库）中保存的文件的换行符都是LF表示的。


## 自己指定换行符转换方案 {#自己指定换行符转换方案}

我们自己在开发过程中，是可以修改/设置Git的换行符转换方案的。修改/设置的方法有2种。


### 通过Git的全局配置进行修改 {#通过git的全局配置进行修改}

设置 `autoclf` 属性，在控制台直接运行如下的一条命令就可以设置了：

```shell
# 提交时转换为LF，检出时转换为CRLF
# 如果是在Windows系统上，把它设置成true（默认配置）
git config --global core.autocrlf true

# 提交时转换为LF，检出时不转换
# Linux或Mac系统使用LF作为行结束符，因此不用Git在签出文件时进行自动的转换；
# 当一个以CRLF为行结束符的文件不小心被引入时需要进行修正，设置成input来告诉Git在提交时把CRLF转换成LF，签出时不转换
git config --global core.autocrlf input

# 提交检出均不转换
git config --global core.autocrlf false
```

上述命令运行之后，会修改家目录 `.gitconfig` 文件。

一般在项目中，为了避免项目中同时出现CRLF和LF，还可以开启 `safecrlf` 检查。当然，如果你的项目自己定义了语法检查规则，例如使用eslint去约束换行符必须是LF，那么当你的文件中出现CRLF的时候，eslint会给你错误提示信息，告诉你不能包含CRLF，这时候，不开启 `safecrlf` 也是可以的 （一般建议开启）。开启方法如下第一条命令：

```shell
# 拒绝提交包含混合换行符的文件 （一般设置为true）
git config --global core.safecrlf true

# 允许提交包含混合换行符的文件
git config --global core.safecrlf false

# 提交包含混合换行符的文件时给出警告
git config --global core.safecrlf warn
```

上述命令运行之后，也会修改 `.gitconfig` 文件。


### 通过.gitattributes进行修改 {#通过-dot-gitattributes进行修改}

参考：<https://git-scm.com/docs/gitattributes>


## 参考资料 {#参考资料}

1.  <https://www.cnblogs.com/wangwenhui/p/12141758.html>
2.  <https://www.cnblogs.com/youpeng/p/11243871.html>
