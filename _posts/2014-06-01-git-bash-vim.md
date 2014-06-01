---
layout: post
title: "��win7��ʹ��gitbash,vim����������������������"
description: ""
category: vim
tags: vim gitbash ����
---
{% include JB/setup %}
###����������
1. gitbash����ls����ֺ�����ʾ���������ʺš�

2. ��vim����һ��file����ʾ���롣

3. ��vim�����뺺����ʾ�ʺš�

��һ�����⣬������git��װĿ¼etc�����޸�һ��������Ϣ��

/etc/git-completion.bash:

alias ls='ls --show-control-chars --color=auto' 

�ڶ������⣬��vim�У�ִ��:set encoding=utf-8 termencoding=gbk fileencoding=utf-8�����Ϳ���������ʾ�ˣ����п������뺺�ֻ��ǻ���ʾ�ʺš�


���������⣬������Ҫ�ռ����������ˣ���/Git/share/vimĿ¼�£��޸�vimrc������

let &termencoding=&encoding

set fileencodings=utf-8,gbk

