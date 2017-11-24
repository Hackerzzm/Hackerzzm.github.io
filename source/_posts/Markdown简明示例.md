---
title: Markdown简明示例
date: 2017-11-24 15:25:57
categories:
- Markdown
tags:
- Markdown

---
**Note:**
这里罗列了Markdown的语法代码，简单粗暴
so... shut up and show me the code!

## 段落、标题、引用
```
A First Level Header
===
A Second Level Header
---

Now is the time for all good men to come to
the aid of their country. This is just a
regular paragraph.

The quick brown fox jumped over the lazy
dog's back.
### Header 3

> This is a blockquote.
> 
> This is the second paragraph in the blockquote.
>
> ## This is an H2 in a blockquote
```
效果:  
由于目录显示问题就不展示了

## 修辞和强调
```
Some of these words *are emphasized*.  
Some of these words _are emphasized also_.  
Use two asterisks for **strong emphasis**.  
Or, if you prefer, __use two underscores instead__.  
```
效果:  
Some of these words *are emphasized*.  
Some of these words _are emphasized also_.  
Use two asterisks for **strong emphasis**.  
Or, if you prefer, __use two underscores instead__.  
## 列表
```
* Candy.
* Gum.
* Booze.

+ Candy.
+ Gum.
+ Booze.

- Candy.
- Gum.
- Booze.
```
效果:  
- Candy.
- Gum.
- Booze.

```
1. Red
2. Green
3. Blue
5. 序号标错了也没关系

```
效果:  
1. Red
2. Green
3. Blue
5. 序号标错了也没关系

```
* A list item.
With multiple paragraphs.插入空行

* Another item in the list.
```
* A list item.
With multiple paragraphs.插入空行

* Another item in the list.

## 链接
```
This is an [example link](http://example.com/).
This is an [example link](http://example.com/ "With a Title").
```
效果:  
This is an [example link](http://example.com/).  
This is an [example link](http://example.com/ "With a Title").
```
I get 10 times more traffic from [Google][1] than from
[Yahoo][2] or [MSN][3].

[1]: http://google.com/ "Google"
[2]: http://search.yahoo.com/ "Yahoo Search"
[3]: http://search.msn.com/ "MSN Search"

I start my morning with a cup of coffee and
[The New York Times][NY Times].

[ny times]: http://www.nytimes.com/
```
效果：  
I get 10 times more traffic from [Google][1] than from
[Yahoo][2] or [MSN][3].

[1]: http://google.com/ "Google"
[2]: http://search.yahoo.com/ "Yahoo Search"
[3]: http://search.msn.com/ "MSN Search"

I start my morning with a cup of coffee and
[The New York Times][NY Times].

[ny times]: http://www.nytimes.com/

## 图片
```
![alt text](http://ozv0hqacp.bkt.clouddn.com/17-11-24/9262387.jpg "Title")
```
效果:  
![alt text](http://ozv0hqacp.bkt.clouddn.com/17-11-24/9262387.jpg "Title")

## 代码
```
I strongly recommend against using any `<blink>` tags.  
I wish SmartyPants used named entities like `&mdash;`
instead of decimal-encoded entites like `&#8212;`.
```
效果:  
I strongly recommend against using any `<blink>` tags.  
I wish SmartyPants used named entities like `&mdash;`
instead of decimal-encoded entites like `&#8212;`.
```
If you want your page to validate under XHTML 1.0 Strict,
you've got to put paragraph tags in your blockquotes:

<blockquote>
<p>For example.</p>
</blockquote>
```
效果:  
If you want your page to validate under XHTML 1.0 Strict,
you've got to put paragraph tags in your blockquotes:

<blockquote>
<p>For example.</p>
</blockquote>

在3个`` ` ``后加入java、javascript、cpp、python或其他语言会代码高亮

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

public class MainActivity extends AppCompatActivity {

  @Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
  }
}
```

## Html锚点页内跳转
实际语法比较简单，在需要跳转的位置添加锚点，语法如下：  
`<span id="jump">跳转到的地方</span>`

在需要点击跳转的位置，使用上面的id，格式类似超链接的形式：  
`[点击跳转](#jump)`

这样就实现了上述功能。

参考链接：  
[Markdown: Basics （快速入门）](http://www.appinn.com/markdown/basic.html "Markdown: Basics （快速入门）")  
[Markdown 语法说明 (简体中文版)](http://www.appinn.com/markdown/#philosophy "Markdown 语法说明 (简体中文版)")