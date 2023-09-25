---
title: markdown语法详解
date: 2023-08-09 11:31:31
tags: [markdown,教程,笔记]
categories: 笔记
cover: https://tse4-mm.cn.bing.net/th/id/OIP-C.6AyAI_1yfgmfWEb69UNerAHaDy?w=305&h=178&c=7&r=0&o=5&dpr=1.1&pid=1.7
---

# markdown常用语法

​		__Markdown 是一种[轻量级标记语言](https://baike.baidu.com/item/轻量级标记语言/52671915?fromModule=lemma_inlink) ，广泛应用于各种文档及博客编写，多的不说，随便在github上搜个项目它的介绍绝对是md文档，又比如 用Word或者别的文本编辑器写好一篇文章，兴高采烈地发布到博客、论坛、网站上时，发现格式完全乱了，于是需要花费大量的时间来重新排版，处理图片、缩进、字体、加粗、标题等。三番五次之后，开始发现文章写作可能只花了半小时，重新排版就花了十多分钟。更让人不悦的是，当我们要把同一篇文章发布到另一个网页上时，这样的排版还要重新做一次。 而使用md完全避免了这些问题，md还是非常好上手的，编辑器也很多，比如vs code，MarkdownPad，BookPad，小书匠，Typora等等，反正一搜一大堆，可以按照自己的喜好使用，我用的是Typora。下面记录的是以前我学习markdown时的一些记录，内容不多但比较实用。__



# 多级标题

用几个#在文字前边就是几级标题，#后面要有`空格`或者ctrl+数字（上边的数字）

```
# 一级标题
## 二级标题
### 三级标题
…… 以此类推
```



# 各种线，脚注

***实现分割线

```
*** 再敲个回车就会出现一条分割线
```

***

***

***

****

_斜体  一个下划线包裹实现斜体_    _斜体字_   

```
_内容_
```

__两个下划线包裹实现粗体__   __粗体__  

```
__内容__
```

___三个下划线包裹实现粗斜体___   ___粗斜体___

```
___内容___
```

~~两个波浪号包裹实现删除线~~   ~~删除线~~

```
~~内容~~
```

<u>u标签实现下划线</u>    <u>U标签</u>

```
<u>内容</u>
```

我是脚注[^1]   【^ 文字】    

[^1]: 最牛了hahha

引用脚注2[^2]

[^2]: 脚注2

```
内容[^脚注]
[^脚注]:脚注内容
```



# 无序列表

使用符号`+`或` -`或` *`，后边跟个空格再加内容

```
+ 内容
- 内容
* 内容

```

+ 哈哈哈


* 哈哈

  

# 有序列表

```
1. 内容     敲回车自动填充下个数字


```



1. 哈哈
   1. 真的不好玩
   2. 是吗

2. 

> 
>
> 用“>”引用

> 1. one
>
>    + one1
>
>    + one2
> 2. two

```
> 内容   内容前边别忘了有空格

```



# 代码块

1. 使用``包起来

   ```
   `内容`
   
   ```

   

   比如`printf`

2. 使用```键

   ```
   输入三个`然后敲回车
   
   ```

   

   ```c
   int main(){
   	printf("hello");
   	return 0;
   }
   
   
   ```

   `代码`

   ```java
   代码块
   public static void main(String[] args){
       System.out.println("Hello World!");
   }
   
   ```

   

# 链接

```
[链接描述](链接地址)
<链接地址>

```

__举例__

```
[百度一下](https://www.baidu.com)

```

[百度一下](https://www.baidu.com)

```
<https://www.baidu.com>

```

<https://www.baidu.com>

<https://www.fosss666.github.io>

```
[跳转到二级标题](#二级标题)

```

[跳转到二级标题](#二级标题)



# 图片

用！【】（）这几个符号

```
![名称（可省）](链接或位置)

```



例： ![百度图片](https://ts1.cn.mm.bing.net/th/id/R-C.2180f1280f61bd3a214d89afb1a8c690?rik=8jCt0oNiYNQJEw&riu=http%3a%2f%2fimg.redocn.com%2fsheying%2f20170502%2fyishinongsuokafei_8173863.jpg&ehk=qDotAPzpCoSJmyRshtxNnnbkiDHDa5FicfgIpe66TG4%3d&risl=&pid=ImgRaw&r=0)

# 表格

使用|来分割

```
 | 编号 | 姓名 | 年龄 |  然后敲回车

```



| 编号 | 姓名 | 年龄 |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |

| 姓名 | 学号 | 年龄 |
| ---- | ---- | ---- |
| 法   |      | d    |
|      |      |      |

# 表情

```
:表情名

```

[表情大全](https://blog.csdn.net/qq_40896997/article/details/106551767)

```
:smile
:cry
:a:

```



:smile:

:cry:

:a:











