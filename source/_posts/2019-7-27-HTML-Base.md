---
title: HTML基础
tags: HTML
comments: true
categories: Web
date: 2019-07-27 23:15:25
---


[TOC]
### 标记文本
#### 块级元素与内联元素
+ 块级元素
页面中以块的形式展示。
相对与其前面的内容它会出现在新的一行，其后的内容也会被挤到下一行展现。块级元素通常用于展示页面上结构化的内容，例如段落、列表、导航菜单、页脚、**div** 等等。

+ 内联元素
内联元素通常出现在块级元素中并包裹文档内容的一小部分，而不是一整个段落或者一组内容。内联元素不会导致文本换行：它通常出现在一堆文字之间。


*注：空元素是没有内容可结束标签的元素。*

#### 文档结构
##### 文档的主要组成部分
+ 页眉（ header 元素）
通常横跨于整个页面顶部有一个大标题 和/或 一个标志。同参存在所有页面中。

+ 导航栏（ nav 元素）
指向网站各个主要区段的超链接。通常用菜单按钮、链接或元素页表示。
类似于标题栏，导航栏通常应在所有网页之间保持一致。

+ 主内容（ main 元素）
中心的大部分区域是当前网页大多数的独有内容，例如视频、文章、地图、新闻等。在一个网站不同页面之间切换时，该部分一般是不同的。主内容中还可以有各种子内容区段，可用 article 、section  和 div  等元素表示。每个页面上只能用一次 main 元素，且直接位于  body元素 中。最好不要把它嵌套进其它元素。

+ 侧边栏（ aside 元素）
一些外围信息、链接、引用、广告等。通常与主内容相关（例如一个新闻页面上，侧边栏可能包含作者信息或相关文章链接）。

+ 页脚（ footer 元素）
横跨页面底部的狭长区域。和标题一样，页脚是放置公共信息（比如版权声明或联系方式）的，一般使用较小字体，且通常为次要内容。 还可以通过提供快速访问链接来进行 SEO。


以上内容 HTML 中的实现：

![页面布局](../images/conventional-html-layout.png@w500@h400)


```HTML
<!DOCTYPE html><html>
  <head>
    <meta charset="utf-8">
    <title>二次元俱乐部</title>
    <link href="style.css" rel="stylesheet">
  </head>

  <body>
    <header> <!-- 本站所有网页的统一主标题 -->
      <h1>聆听电子天籁之音</h1>
    </header>
    
    <nav> <!-- 本站统一的导航栏 -->
      <ul>
        <li><a href="#">主页</a></li>
        <!-- 共n个导航栏项目，省略…… -->
      </ul>

      <form> <!-- 搜索栏是站点内导航的一个非线性的方式。 -->
        <input type="search" name="q" placeholder="要搜索的内容">
        <input type="submit" value="搜索">
      </form>
    </nav>
    
    <main> <!-- 网页主体内容 -->
      <article>
        <!-- 此处包含一个 article（一篇文章），内容略…… -->
        <section>
        <!--也可以把一篇 <article> 分成若干部分并分别置于不同的 <section> 中，也可以把一个区段 <section> 分成若干部分并分别置于不同的 <article> 中-->
        </section>
       
      </article>
      
      <aside> <!-- 侧边栏在主内容右侧 -->
        <h2>相关链接</h2>
        <ul>
          <li><a href="#">这是一个超链接</a></li>
          <!-- 侧边栏有n个超链接，略略略…… -->
        </ul>
      </aside>
    </main>
    
    <footer> <!-- 本站所有网页的统一页脚 -->
      <p>© 2050 某某保留所有权利</p>
    </footer>
  </body></html>
```

##### HTML 布局元素细节

**无语义元素**

对于一些要组织的项目或要包装的内容，现有的语义元素均不能很好对应。有时候你可能只想将一组元素作为一个单独的实体被 CSS 修饰或 Javascript 操作。为了应对这种情况，HTML提供了 div 元素 和 span 元素。应配合使用 class 属性提供一些标签。

+ span 元素是一个内联的（inline）无语义元素，最好只用于无法找到更好的语义元素来包含内容时，或者不想增加特定的含义时。
+ div 元素是一个块级无语义元素，应仅用于找不到更好的块级元素时，或者不想增加特定的意义时。

**换行与水平风格线**

br 元素可在段落中进行换行；br 元素是唯一能够生成多个短行结构的元素。

hr 元素元素在文档中生成一条水平分割线，表示文本中主题的变化（例如话题或场景的改变）。一般就是一条水平的直线。


#### 文字排版
##### 基本文字处理
**标题和段落**
+ 编辑结构层次
标题级别在每个页面不要超过三个。并且文本内容使用标题和段落进行结构化，这样有利于快速浏览以查找相关内容。标题有利于 SEO。有利于样式化和操作。
+ 语义的重要性
使用了正确的元素来给予内容正确的意思、作用以及外形。
+ 强调
在HTML中用 em 元素（emphasis）元素来标记需要强调（需要重读的内容）的文本。用斜体显示。
+ 非常重要
在HTML中用 strong 元素 (strong importance) 元素来标记重要（需要重音的内容）的文本。用粗体表示。
+ 斜体字、粗体字、下划线
i 元素, b 元素 和 u 元素出现于要在文本中使用斜体、粗体、下划线，但CSS仍然不被完全支持的时期。像这样的元素，仅仅影响表象而且没有语义，被称为表象元素（presentational elements）并且**不应该再被使用**。因为语义对可访问性，SEO（搜索引擎优化）等非常重要。

**基本列表**
+ 无序列表
无序的列表被用来标记每个项目。每份无序的清单从 ul 元素开始，接着用 li 元素把每个列出的项目分别包裹起来。
+ 有序列表
有序的列表是根据项目的顺序列出来的。标记的结构和无序列表一样，除了你需要用 ol 元素将所有项目包裹。


##### 高级文字处理

**描述列表**
描述列表的目的是标记一组项目及其相关描述。描述列表使用与其他列表类型不同的闭合标签— dl 元素; 此外，每一项都用 dt (description term) 元素闭合。每个描述都用 dd (description description) 元素闭合。

```HTML
<dl>
  <dt>内心独白</dt>
    <dd>戏剧中，某个角色对自己的内心活动或感受进行念白表演</dd>
  <dt>语言独白</dt>
    <dd>戏剧中，某个角色把自己的想法直接进行念白表演。</dd>
  <dt>旁白</dt>
    <dd>戏剧中，为渲染幽默或戏剧性效果进行的场景之外的补充注释念白</dd></dl>
```

**引用**

+ 块引用
如果一个块级内容（一个段落、多个段落、一个列表等）从其他地方被引用，你应该把它用blockquote 元素包裹起来表示，并且在 cite 属性里用 URL 来指向引用的资源。

```HTML
<blockquote cite="https://baidu.com"><p>Hello world</p></blockquote>
```

+ 行内引用
行内元素使用 q 元素表示。

```HTML
<p><q cite="https://baidu.com">Hello world</q></p>
```


+ 引文
cite 属性的内容听起来很有用，但如果不使用 JavaScript 或 CSS，浏览器不会真的关心它，不会显示 cite 的内容。如果你想要确保引用的来源在页面上是可用的，更好的方法是把 cite 元素放到引用元素旁边。这就意味着包含引用来源的名称——即引用的书的名字。

```HTML
<p>The quote element <q cite="https://baidu.com"> intended for short quotations .</q>-- <a href="https://baidu.com"><cite>MDN q page</cite></a></p>
```


+ 缩略语
abbr 元素常被用来包裹一个缩略语或缩写，并且提供缩写的解释（包含在title属性中）

```HTML
<p>我们使用 <abbr title="超文本标记语言（Hypertext Markup Language）">HTML</abbr> 来组织网页文档。</p>
```


+ 标记联系方式
HTML使用 address 元素标记联系方式。

```HTML
<address>
  <p>Chris Mills, Manchester, The Grim North, UK</p>
 </address>
```

+ 上标和下标
 sup 元素表示上标，sub元素 表示下标。

 ```HTML
<p>咖啡因的化学方程式是 C<sub>8</sub>H<sub>10</sub>N<sub>4</sub>O<sub>2</sub>。</p>
<p>如果 x<sup>2</sup> 的值为 9，那么 x 的值必为 3 或 -3。</p>
 ```

+ 计算机代码
code 元素用于标记计算机通用代码。

+ 时间和日期
HTML 使用 time 元素将时间和日期标记为可供机器识别的格式。

```HTML
<time datetime="2016-01-20">2016年1月20日</time>
```

### 链接
##### 链接的解析
通过将文本转换为 a 元素内的链接来创建基本链接， 给它一个 href 属性（也称为目标），它将包含您希望链接指向的网址.

+ 链接添加 title 属性支持。
含关于链接的补充有用信息

+ 块级链接
通过将图像元素（img 元素）嵌套在链接元素(a 元素)中间，将一个连接转化为一个块元素。


##### 统一资源定位符(URL)与路径(path)


**文档片段**
超链接除了可以链接到文档外，也可以链接到HTML文档的特定部分（被称为文档片段）。要做到这一点，你必须首先给要链接到的元素分配一个id属性。

```HTML
<h2 id="Mailing_address">Mailing address</h2>
<p>Want to write us a letter? Use our <a href="contacts.html#Mailing_address">mailing address</a>.</p>
```

**绝对URL和相对URL**
+ 绝对URL
指向由其在 Web 上的绝对位置定义的位置，包括 protocol(协议) and domain name（域名）。
+ 相对URL
指向与您链接的文件相关的位置，更像我们在前面一节中所看到的位置。

**链接最佳实践**
+ 尽可能使用相对链接
当链接到同一网站的其他位置时，你应该使用相对链接（当链接到另一个网站时，你需要使用绝对链接）
+ 链接到非html资源 ——留下清晰的指示

```HTML
<p><a href="http://www.example.com/large-report.pdf">
  Download the sales report (PDF, 10MB)
</a></p>

<p><a href="http://www.example.com/video-stream/">
  Watch the video (stream opens in separate tab, HD quality)
</a></p>

<p><a href="http://www.example.com/car-game">
  Play the car game (requires Flash)
</a></p>
```

+ 下载时使用 download 属性
当您链接到要下载的资源而不是在浏览器中打开时，您可以使用 download 属性来提供一个默认的保存文件名。

**电子邮件链接**
当点击一个链接或按钮时，打开一个新的电子邮件发送信息而不是连接到一个资源或页面。
使用 a 元素和 mailto：URL的方案。其最基本和最常用的使用形式为一个mailto:link （链接），链接简单说明收件人的电子邮件地址。
其中最常用的是主题(subject)、抄送(cc)和主体(body) (这不是一个真正的头字段，但允许您为新邮件指定一个短内容消息)

```HTML
<a href="mailto:nowhere@mozilla.org">Send email to nowhere</a>

<a href="mailto:nowhere@mozilla.org?cc=name2@rapidtables.com&bcc=name3@rapidtables.com&subject=The%20subject%20of%20the%20email&body=The%20body%20of%20the%20email">
  Send mail with cc, bcc, subject and body
</a>
```

#### HTML 错误排查

+ [HTML 验证](https://validator.w3.org/)

网页可以接受网址、上传一个 HTML 文档，或者直接输入一些 HTML 代码;能够判断所上传代码是否有语法错误，对于逻辑错误，这种方式无法解决。

### 多媒体与嵌入
#### HTML中的图片
通过 img 元素将图片放到网页上。smg 是个空元素，最少只需要一个 src 属性。src 属性包含了指向我们想要引入的图片的路径，可以是相对路径或绝对URL。使用绝对路径会导致浏览器去做更多的工作（例如 通过 DNS 解析域名）。

**网页中的图片**

+ 备选文本
 img 元素的 alt 属性，可以为图片添加文字描述。
+ 高度和宽度
width：宽度
height：高度
+ 图片标题
类似于超链接，你可以给图片增加title属性来提供需要更进一步的支持信息。

**解说图片**
可以通过图片搭配文字描述的方式解说图片。使用  HTML5 的 figure 元素和 figcaption 元素可以实现这个功能。

```HTML
<figure>
  <img src="https://baidu.com/dinosaur_small.jpg"
     alt="一只恐龙头部和躯干的骨架，它有一个巨大的头，长着锋利的牙齿。"
     width="400"
     height="341">
  <figcaption>曼彻斯特大学博物馆展出的一只霸王龙的化石</figcaption>
</figure>
```

+ figure
figure 里不一定要是一张图片，只要是一个这样的独立内容单元。figure 可以是几张图片、一段代码、音视频、方程、表格或别的。
+ figcaption
figcaption 元素 告诉浏览器这段说明文字描述了 figure 元素的内容.

#### 视频与音频
**video 标签** 
video 允许你简单的嵌入一段视频。

```HTML
<video src="rabbit320.webm" controls>
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
 </video>
```

+ src 属性
指向你想要嵌入网页当中的视频资源，他们的使用方式完全相同。
+ controls 属性
用户必须能够控制视频和音频的回放功能。你可以使用浏览器提供的控制接口，同时你也可以使用合适的 JavaScript API构建控制接口。
+ video 标签内的段落
这个叫做后备内容 — 当浏览器不支持 video 元素标签的时候，它将会显示出来，它使我们能够对旧的浏览器做一些兼容处理。

**多格式支持**

```HTML
<video controls>
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
 </video>
```
将 src 属性从 video 元素中移除，转而将它放在几个单独的标签 source 元素当中。每个 source 元素标签页含有一个 type 属性，这个属性是可选的，但是建议你添加上这个属性 — 它包含了视频文件的 MIME types。

**其他 video 属性**

+ width 和 height
控制视频尺寸
+ autoplay
该属性设置音频和视频内容立即播放，即使页面的其他部分还没有加载完全。建议不要设置该属性。
+ loop
该属性可以让音频或者视频文件循环播放。
+ muted
该属性会导致媒体播放时，默认关闭声音。
+ poster
该属性指向了一个图像的URL，这个图像会在视频播放前显示。通常用于粗略的预览或者广告。
+ preload
该属性被用来缓冲较大的文件，有3个值可选：
    1.none" ：不缓冲
    2."auto" ：页面加载后缓存媒体文件
    3."metadata" ：仅缓冲文件的元数据

**audio 标签**
audio 标签与 video 标签的使用方式几乎完全相同，有一个细微的差别在于的边框不同。

```HTML
<audio controls>
  <source src="viper.mp3" type="audio/mp3">
  <source src="viper.ogg" type="audio/ogg">
  <p>你的浏览器不支持 HTML5 音频，可点击<a href="viper.mp3">此链接</a>收听。</p>
 </audio>
```

#### Iframe


### 表格

### 表单