---
layout: post
title: "学习Markdown,this is a test。"
description: ""
category: learning
tags: markdown test
---
{% include JB/setup %}
###怎么学markdown呢，我的办法是打算把github guides 上面的*Mastering Markdown*上面的例子敲打一遍-_-！。
It's very easy to make some words **bold** and other words *italic* with Markdown. You can even [link to Google!](http://google.com).
It's very easy to make some words **bold** and other words *italic* with Markdown. You can even [link to Baidu!](http://www.baidu.com).
###终于把字符乱码的问题搞定了，明天可以用vim编辑，直接push上去了。。。-_-!
那今天继续敲。
####Lists
Sometimes you want numbered lists:
1. One
2. Two
3. Three

Sometimes you want bullet points:
* Start a line with a star
* Profit!

Alternatively,

- Dashes work just as well
- And if U have sub points, put two spaces before the dash or star:
  - Like this
  - And this
####Images

If you want to embed images, this is how you do it:
![Image of Yaktocat](http://octodex.github.com/images/yaktocat.png)

####Headers&Quotes

# Structured documents

Sometimes it's useful to have different levels of headings to  structure your
documents. Start lines with a '#' to create headings. Multiple '##' in a row 
denote smaller heading sizes.

### This is a third-tier heading

You can use one '#' all the way up to '######' six for different heading sizes.

If you'd like to quote someone, use the > character before the line:

> Coffee. The finest organic suspension ever devised... I beat the Borg with it.> - Captain Janeway

#### Code 

There are many different ways to style code with GitHub's markdown. If you
have inline code blocks, wrap them in backticks: 'var example = true'. If
you've got a longer block of code, you can indent with four spaces:

    
    if (isAwesome){
        return true
    }

GitHub also supports something called code fencing, which allows for multiple
lines without indentation:

'''

if (isAwesome){
    return true
}

'''

And if you'd like to use syntax highlighting, include the language:

'''javascript

if (isAwesome){
    return true
}

'''

#### Extras

GitHub supports many extras in Markdown that help you reference and 
link to people. If you ever want to direct a comment at someone, you
can prefix their name with an @ symbol: Hey @yourmoonlight - welldown!

But I have to admit , tasts lists are my favorite:

- [x] This is a complete item
- [ ] This ia an incomplete item

And, of course emoji! :sparkles: :camel: :boom:

