---
layout: post
title: "在win7下使用gitbash,vim遇到的乱码问题解决方案"
description: ""
category: vim
tags: vim gitbash 乱码
---
{% include JB/setup %}
###遇到的问题
1. gitbash下用ls命令发现汉字显示出来的是问号。

2. 用vim打开一个file，显示乱码。

3. 在vim中输入汉字显示问号。

第一个问题，可以在git安装目录etc下面修改一下配置信息。

/etc/git-completion.bash:

alias ls='ls --show-control-chars --color=auto' 

第二个问题，在vim中，执行:set encoding=utf-8 termencoding=gbk fileencoding=utf-8这样就可以正常显示了，但有可能输入汉字还是会显示问号。


第三个问题，这就需要终极解决方案了，在/Git/share/vim目录下，修改vimrc，添加

let &termencoding=&encoding

set fileencodings=utf-8,gbk

