在 css 中实现垂直居中很多时候会用到 vertical-align ，不过我一直对 vertical-align 的使用糊里糊涂，现在整理一下关于它的一些知识点。[原文链接][1]


### 1. 适用元素

vertical-align属性只能适用于内联元素( inlcine )、内联块元素( inline-block )和table-cell元素( td )，包括 display 属性为 inline 、inline-block 和 table-cell 的元素。由于元素在浮动和绝对定位之后会自动转换为块元素( block )，所以浮动和绝对定位元素也没有 vertical-align 属性。

### 2. 取值

关于文本的顶线、中线、基线和底线已经在<a href="http://www.zengkan.win/?p=84">介绍行高的文章中</a>的配图中解释过了。

line box 的基线等于这一行中没有经过任何人为对齐设置的文本的基线。

inline block 元素的基线位置，在<a href="https://www.w3.org/TR/CSS2/visudet.html">css中规定</a>是：


>  The baseline of an 'inline-block' is the baseline of its last line box in the normal flow, unless it has either no in-flow line boxes or if its 'overflow' property has a computed value other than 'visible', in which case the baseline is the bottom margin edge.
  
>  inline-block 元素的基线是这个元素下正常文档流中最后一个 line box 的基线，除非这个元素中没有正常流的 line box 或者本身的 overflow 属性值不是默认的 visible ，这个时候 inline-block 元素的基线位置是margin的底边缘。


以下是 <a href="https://www.w3.org/TR/CSS22/visudet.html#leading">w3c 中关于vertical-align取值的规定</a>：

1. baseline: 默认值，盒子的基线与父级盒的基线对齐。如果这个盒子没有基线，就让这个盒子的 margin 底边缘和父级盒基线对齐。
2. middle: 将盒子的垂直中心点与父级盒子基线往上 X 一半高度的位置对齐。
3. sub： 降低盒子的基线到父级盒合适的下标位置。
4. super： 提高盒子的基线到父级盒合适的上标位置。
5. text-top: 将盒子的顶部（top）与父级盒子内容区域的顶部对齐。
6. text-bottom: 将盒子的底部（bottom）与父级盒子内容区域的底部对齐。
7. %: 将盒子相对基线位置上移或下移多少位置（百分比的值时相对于行高 line-height ），" 0% "时位置与设置 baseline 一致。
8. 数值： 将盒子相对基线位置上移或下移多少位置。0px 时位置与设置 baseline 一致。
9. top: 将盒子的顶部与行框 line box 的顶端对齐（行框的顶部就是这一行行内框的最高点）。
10. bottom: 将盒子的底端与 行框 line box 的底端对齐（行框的底端就是这一行行内框的最低点）。
以上前8个的取值是相对于盒子的<b>父级盒子</b>，后2个取值是相对于这一行的<b>行框 line box </b>。从之前<a href="http://www.zengkan.win/?p=84" target="_blank">行高的文章</a>我们知道了盒子的顶部与父级盒子内容区顶部的间距就是半行距，当盒子的字体大小等于 line-height 大小时，盒子和父级盒子的内容区域会重叠，也就是下面 demo 中 表示 text-top 和 text-bottom 的线会与表示 top 和 bottom 的线重合。

### 3. 示例

下面 demo 来自<a href="http://christopheraue.net/2014/03/05/vertical-align/" target="_blank"> Vertical-Align: All You Need To Know</a>。便于理解各种取值之间的关系。
[verrical-align 参考线模型][2]

上面 demo 中，不同颜色的高度为 0 的线设置不同的 vertical-align 值，利用 margin-right 负值将所有的线和最后一个行块放置在同一个行框 line box 中。

刚开始看到这个 demo 的时候，完全不清楚为什么这几个宽度和父级一样线能够放在同一个行框中，了解之后知道是 margin 负值的作用， margin 负值的相关知识在文章最后。

demo 中的所有元素都处在同一个行框 line box 中，这几根线表示这个行框的各个参考线。六个灰块分别设置不同的 vertical-align 值来与父元素对齐。仔细观察

1. 基线对齐是子元素的基线和父元素的基线对齐，demo 中这个子元素的基线是末尾的X的下边缘。
2. 仔细测量居中对齐块的上下距离之后我们会发现，这个块并不是完全得居中对齐。这并不是 bug ，而是安全按照 w3c 的规定：“盒子的垂直中心点与父级盒子基线往上 X 一半高度位置对齐”， middle 参考线也穿过最左边 X 的中心，而字符具有下沉特性，所以 X 的中心并不是字符所在行内框的中心点，也就不在整个行框的中心点。我们可以将 font-size 设为 0 来让元素完全垂直居中。

### 4. 应用
#### 4.1 单选框复选框或小图标和文字的对齐
[对齐小图标][3]
由于 vertical-align 为 middle 并不能让元素完全居中，会受到字体大小的干扰，在这种小图标的对齐上使用 middle 会使得这种干扰更明显，所以设置像素值让小图标相对于基线偏移的做法更准确一些。
#### 4.2 不固定高度的图片的垂直居中（方法1）
[查看 demo][4]（测试发现预览中的按钮点击无效，请直接点击原链接测试）
上面 demo 中，当我们改变图片的 vertical-align 属性值时，发现整个行框的基线位置也发生了变化。这是因为这个行框中最高点和最低点都在图片上，所以行框的高度就等于这个图片的高度，当图片的 vertical-align 值发生变化时，行框内没有足够的空间可以让图片移动，所以只能移动基线。

而且当图片的 vertical-align 值设置为 middle 时，图片并没有在父级中垂直居中，这是因为图片只是在所在行框中近似垂直居中，要想让图片在父级中居中，可以让行框的高度等于父级盒子的高度。也就是可以让父级的 line-height 等于高度。
[图片垂直居中][5]
字体大小设置为 0 是让元素完全垂直居中
在上面的例子中，有没有文本标签都没有区别，我们可以看做父级盒子中有一个空白的文本节点来作为基线位置的参考。
#### 4.2 不固定高度的图片的垂直居中（方法2）
方法2[链接描述][6]
上面 demo 的方法创建一个高度为 100% 的伪元素（空元素也可以），这样这个元素的基线就会和父级的基线重合，空元素的垂直中心点也与父级的中心重合，图片也会垂直居中。
#### 4.3不固定高度的文本的垂直居中
[文本垂直居中][7]
垂直居中文本的方法和图片的方法差不多，文本标签 inline-block 化，然后按照居中图片的方法来居中。其中需要注意的是父级元素设置 font-size 为 0 ，这是为了消除伪元素（或者空元素）与文本标签之间的间隙，如果这个间隙存在，那么这个伪元素会换行，不和文本元素在同一个行框中，垂直居中就会失效。
### 5. margin 负值的应用（来自 <a href="http://www.hicss.net/i-know-you-do-not-know-the-negative-margin/" target="_blank">我知道你不知道的负 margin</a>）

margin 的四个取值中，本质上虽然都是在和其他元素之间产生间距，但实现的方法却不一样。其中 left 和 top 值是相对相邻元素的边界改变自己的位置，而 right 和 bottom 是相对元素本身位置来影响周边元素的位置（实际上是影响右边和下边相邻元素的参考线）。

当 margin 值为正值时，会按照正常逻辑向元素四周产生间距。当元素的 margin-left 和 margin-top 为负值时，元素会向左或向上移动，当元素的 margin-right 和 margin-bottom 为负值时，<strong>元素右边和下边的元素的参考线位置会变化</strong>。

上面的 demo 中，六条参考线的宽度都与父元素一致，所以默认会处在六个不同的行框 line box 中。但给他们都设置了 margin-right 负值，这个负值足够小的话，<strong>会将下一行的元素拉上来</strong>。

正常情况下 margin-right 的值等于负的元素宽度就可以实现，不过六个 inline-block 元素之间默认会有间隙，我们可以把代码写成一行来避免这个间隙的出现，不过可读性就比较差，这个<strong>间隙的大小默认是和父元素的字体大小有关</strong>的，也可以把父元素 font-size 设为 0 来消除间隙，不过这样就会导致几根参考线重合，影响表达效果。所以上面 demo 中线元素的 margin-right 值的绝对值都稍大于元素的宽度，这是为了抵<strong>消正常流中 inline-block 元素之间的间隙</strong>。


  [1]: http://www.zengkan.win/?p=168
  [2]: http://codepen.io/zengkan0703/pen/EZmwOO
  [3]: http://codepen.io/zengkan0703/pen/YNVMxe
  [4]: http://codepen.io/zengkan0703/pen/EZmzYw
  [5]: http://codepen.io/zengkan0703/pen/MJmdQE
  [6]: http://codepen.io/zengkan0703/pen/qRmwVj
  [7]: http://codepen.io/zengkan0703/pen/rjmgbr