---
layout: post
title: "批量重命名字幕文件"
description: ""
category: learning
tags: python 重命名
---
{% include JB/setup %}

###程序员都是"懒"家伙吧
今天从网上下载一些英文视频教程， 虽然文件不大，但是分成了很多小节，一节几分钟的样子，
这样每过几分钟就得手动添加字幕，好麻烦。我就想为什么播放器不能自动加载字幕呢，我都已经把
字幕跟视频放在一个文件夹了，播放器应该有这个功能吧？上网搜索了一下果然有这个功能，只是需
要字幕文件跟视频文件同名才能自动加载。对比了下字幕和视频的文件名，区别只是字幕文件名多了
个".en"。要手动改么？当然不要，有上百个字幕文件，太麻烦。
那么下面，就稍微动动手吧。

###解决方案
１．把文件夹里的所有文件名都列出来。

２．把其中.srt结尾的字幕文件找出来。

３．给它重命名。

依着这个步骤，谷歌了一下，每一步需要用到的function，也就找出来了，再看
看Python Doc，就知道怎么用了。 下面是代码：

```python
# -*- coding: utf-8 -*-
#如果让KMPlayer自动加载字幕，则需要字幕文件名跟video文件名一样。
#字幕文件太多了，手动怎么改的过来？所以有了下面的这个小程序。

import os

#这个路径前面要加r, 不然会出现error-123.
path = r"x:\xxxxxx\xxxxx\xxxxxx\xxxxx"
for file in os.listdir(path):
	if file.endswith(".srt"):
		#print file
		newname = file.replace(".en.srt", ".srt")
		#print newname
		os.rename(os.path.join(path, file), os.path.join(path, newname))		
```
 
	
