---
layout: post
title: "在win7下使用gitbash，vim遇到的乱码问题"
description: ""
category: vim
tags: vim gitbash 
---
{% include JB/setup %}
###遇到的问题
1. gitbash中使用ls命令汉字会显示成问号

2. 使用vim打开一个file会显示乱码

3. vim中显示正常但是输入汉字却是问号

第一个问题，可以修改以下路径的file

/etc/git-completion.bash:

添加 alias ls='ls --show-control-chars --color=auto' 

第二个问题在vim中输入这个命令:set encoding=utf-8 termencoding=gbk fileencoding=utf-8


第三个问题修改/Git/share/vim目录中的vimrc 添加这两行

let &termencoding=&encoding

set fileencodings=utf-8,gbk

