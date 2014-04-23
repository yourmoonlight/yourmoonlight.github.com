---
layout: post
title: "yourmoonlight"
description: ""
category: 杂记
tags: [intro]
---
{% include JB/setup %}
#一级标题
	
	这是用jekyll写的，试着用markdown语法。
	* 第一点
	* 第二点
	* 第三点

***

##二级标题
	
	balabalabala
	- first
	- second
	- third


{% highlight ruby linenos %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}
