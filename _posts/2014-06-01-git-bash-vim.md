---
layout: post
title: "win7下gitbash，vim乱码问题解决方案"
description: ""
category: vim
tags: vim gitbash win7
---
{% include JB/setup %}
###使用gitbash,vim遇到的乱码问题
1. 使用ls命令发现汉字显示成问号

2. 在gitbash里使用vim打开file，汉字全是乱码

3. vim中显示都正常了，但是输入汉字会显示为问号

对于第一个问题，在git安装目录etc下面，添加：

alias ls='ls --show-control-chars --color=auto'

第二个问题，在vim中执行:set encoding=utf-8 termencoding=gbk fileencoding=utf-8

第三个问题，有时候虽然显示都正常了，但是输入汉字会变成问号。

let &termencoding=&encoding

set fileencodings=utf-bom,utf-8,gbk,gb2312,gb18030,cp936,latin1

在Git\share\vim\vimrc中添加这俩行后应该就行了。


###总结

不同的使用环境不一样，网上的一些解决方法也不一定具有通用性，

vim乱码与系统环境是有关的，只是从网上找答案，而不去分7自己

系统的使用环境，会弄得很糊涂，网上有一篇帖子说的很好，感兴趣

的话这是链接[vim字符编码终极方案](http://blog.zheezes.com/vim-ultimate-character-encoding-scheme.html
)

