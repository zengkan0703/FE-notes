今天在解决 ios 移动端滚动穿透的问题时遇到一个问题，就是判断元素能否滚动，把这个过程记录下来。以下以纵向滚动为例，横向滚动同理。
嫌麻烦的可以直接[查看代码](https://gist.github.com/zengkan0703/d294f60b7133555803f8dd3b12c086e9)。

#### 基础概念
**Element.scrollHeight**
> scrollHeight 是一个元素内容高度的度量，包括由于溢出导致的视图中不可见的内容，scrollHeight 的值等于元素在不能出现滚动条的时候展示出视图中所有内容所需要的最小高度。
> 如果元素内容能在不出现滚动条的情况下全部展示，那么元素的 scrollHeight 和 clientHeight 相等。[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollHeight)

**Element.clientHeight**
>clientHeight 是指元素内部高度，包括 padding，但不包括 margin、border 以及 横向滚动条。
>clientHeight = CSS height + CSS padding - height of horizontal scrollbar (if present)[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientHeight)

#### 第一个方案
根据上面两个定义，很容易就想到可以通过比较 scrollHeight 和 clientHeight 的大小来判断元素是否可以滚动。最开始我就是这么做的。
```javascript
function eleCanScroll(ele) {
  return ele.scrollHeight > ele.clientHeight; 
}
```
使用之后发现这个方法大部分时候判断是对的，但是在一些情况下就出现的偏差。
**偏差1**
[查看demo](https://codepen.io/zengkan0703/pen/eYNGqxV)
子元素超出父元素，而且父元素不设置 `overflow: auto`
**偏差2**
[查看demo](https://codepen.io/zengkan0703/pen/RwPjbaW)
子元素相对父元素绝对定位

上面的两个 demo 中，虽然 box 元素的 scrollHeight 大于 clientHeight，但是并不能滚动。

其实原因从 scrollHeight 的定义中就能找到，**scrollHeight 是一个元素内容高度的度量，包括由于溢出导致的视图中不可见的内容**，也就是说，只要元素的子元素没有完全在父级内容框中显示出来，不管是因为定位还是 translate 偏移导致，父级元素的 scrollHeight 就一定大于 clientHeight。而这里面很多情况下父级元素都是不能滚动的。

所以通过比较 scrollHeight 和 clientHeight 的大小来判断元素是否可以滚动的方法是错误的，我看网上有的解决办法是判断父级元素的 overflow 属性，但我们知道这样也是不对的。

#### 解决方案
正确的解决办法就要从 scrollTop 上入手了。
**Element.scrollTop**
> 一个元素的 scrollTop 值是这个元素的顶部到视口可见内容（的顶部）的距离的度量。当一个元素的内容没有产生垂直方向的滚动条，那么它的 scrollTop 值为0。[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTop)

> scrollTop 可以被设置为任何整数值，同时注意：如果一个元素不能被滚动（例如，它没有溢出，或者这个元素有一个"non-scrollable"属性）， scrollTop**将被设置为0**。

根据上面 scrollTop 的语法我们就能找到解决方案。我们可以设置元素的 scrollTop = 1，再获取下 scrollTop，如果值变为 0，说明该元素不可以滚动。
[代码地址](https://gist.github.com/zengkan0703/d294f60b7133555803f8dd3b12c086e9)
```javascript
function eleCanScroll(ele) {
  if (!ele instanceof HTMLElement) {
    console.log("fuck off");
    return;
  }
  if (ele.scrollTop > 0) {
    return true;
  } else {
    ele.scrollTop++;
    // 元素不能滚动的话，scrollTop 设置不会生效，还会置为 0
    const top = ele.scrollTop;
    // 重置滚动位置
    top && (ele.scrollTop = 0);
    return top > 0;
  }
}
```
#### 参考文档
https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTop
https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollHeight
https://developer.mozilla.org/en-US/docs/Web/API/Element/clientHeight