整理一下关于行高的一些基本概念。

### 1.定义

每个文本行中可以看做有四条线：顶线，中线，基线和底线。类似英语作业本上的“四线三格”。如下图（图片来自[@梦禅的文章](https://segmentfault.com/a/1190000003038583)）![image](http://www.zengkan.win/wp-content/uploads/2017/01/line-height.png)
line-height 行高就是指两个文本行相同线之间的距离。同一行的顶线到底线的距离就是内容区域高度，**内容区域的高度只与字体和字号有关**，在有的字体(simsun等)中，字体大小等于内容区域高度。上一行的底线和下一行的顶线的距离是行间距。可以看出，**行高 = 内容高度+行间距**，当我们改变 line-height 的值时，内容高度是不变的，改变的只是行间距，从而改变了行高。
### 2.高度
一个空元素的高度默认是0，而当这个元素中有内容时，元素就会被撑开，会有高度。之前我以为这个高度是文字的高度撑开的，而从[@张鑫旭的文章](http://www.zhangxinxu.com/wordpress/2009/11/css%E8%A1%8C%E9%AB%98line-height%E7%9A%84%E4%B8%80%E4%BA%9B%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%E5%8F%8A%E5%BA%94%E7%94%A8/)中知道这个高度是由 line-height撑开的。以下代码结果如下：
```
<div style="background:#999;line-height:1px;font-size:20px;">行高为1</div>
<div style="background:#999;line-height:20px;font-size:0;">行高为默认</div>
```
![image](http://www.zengkan.win/wp-content/uploads/2017/01/line-height2.jpg)

第一个 div 的行高为1像素，字体大小为20像素，最终高度为1像素；
第二个 div 行高为20像素，字体大小为0，最终高度是20像素。说明在没有设置固定高度的元素中，高度并不是由文字撑开，具体高度是怎么产生的，先了解下面的几个概念。
### 3.行内框盒模型
![图片描述][1]
1. **行内框 inline boxes** ：行框下面的每一个标签（包括文本标签）都是一个单独的行内框。行内框的高度由行高line-height决定。
2. **行框line boxes** ：每行文字都是一个行框，行框包含这一行行内框的最高点和最低点。行框的高度是这一行行内框的最高点到最低点的距离。<a href="https://www.w3.org/TR/CSS22/visudet.html#leading" target="_blank">The line box height is the distance between the uppermost box top and the lowermost box bottom.</a>
3. **包含盒子containing box**：元素中行框的高度垂直叠加形成包含盒子的高度，也就是元素的高度。

所以在没有设置固定高度的元素中，高度是由这个元素中所有行框高度叠加而成的。

**提示：** 有种说法是行框的高度等于这一行中行内框中最大的line-height，这种说法是错误的。
```
<div style="line-height:30px">
    行内框1,行高30px<span style="line-height:50px;background:yellow;vertical-align:-20px">行内框2,行高50px,</span>        
</div>
```
![图片描述][2]

上述代码结果如上图，这个 div 中的行内框最大的行高是50像素，但由于这个行内框存在 vertical-align 偏差，所以这个行框的高度大于50像素。也就是**行框的高度是这一行行内框的最高点到最低点的距离**。

### 4.继承
line-height 属性是可以继承的，不过继承规则因父元素的取值而异。具体如下表：
![image](http://www.zengkan.win/wp-content/uploads/2017/01/table-1.jpg)
 值得注意的是，父元素的 line-height 值设置为130%或1.3对本身来说是一样的，但对于下面的子元素来说两种设置方式的继承规则不一样，所以继承到的行高也是有差别的。

### 实用tip
![图片描述][3]

上面图片中，外面元素没有设置height，由内容撑开，但是图片下方默认会有一些间距。这个间距产生的原因：图片和文字是同一个行框下的行内框，二者默认基线对齐，下面的边距其实是基线和底线之间的距离。当父元素中只有图片这一个元素时，也会有这个问题，可以看做是元素内部默认会有一个空白的文字节点。

解决办法：
1. 设置父元素 line-height 为0，基线和底线就会重合，间距就会消失。
2. 图片设置 vertical-aligh 为 bottom ，底线对齐。
3. 图片设置 display 为block，因为块元素没有vertical-align属性，所以不存在基线对齐。

上面三种办法中，1和3会影响父元素内的文字状态，所以第二种办法比较好。


  [1]: /img/bVHOTZ
  [2]: /img/bVHOT2
  [3]: /img/bVHOTx