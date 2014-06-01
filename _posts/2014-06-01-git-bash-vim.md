---
layout: post
title: "win7下用gitbash,vim乱码问题解决方案"
description: ""
category: vim
tags: vim gitbash 
---
{% include JB/setup %}
###����������
1. gitbash��ʹ��ls����ֻ���ʾ���ʺ�

2. ʹ��vim����һ��file����ʾ����

3. vim����ʾ�����������뺺��ȴ���ʺ�

��һ�����⣬�����޸�����·����file

/etc/git-completion.bash:

���� alias ls='ls --show-control-chars --color=auto' 

�ڶ���������vim��������������:set encoding=utf-8 termencoding=gbk fileencoding=utf-8


�����������޸�/Git/share/vimĿ¼�е�vimrc ����������

let &termencoding=&encoding
   
set fileencodings=utf-8,gbk
���
