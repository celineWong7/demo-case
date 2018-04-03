# demo-case
一些特殊样式、特殊处理的小demo整合
#### 1. 文字溢出的处理：text-flow属性

我们经常遇到大段文字在显示的时候，希望之显示部分，其他先用省略号（或其他）表示，当点击“查看详情”或者是点击省略号显示剩余的文本。这个时候可以考虑用CSS中的text-flow属性来实现。注意要结合white-space属性一起使用。
```html
.text-overflow {
    overflow: hidden;
    text-overflow: ellipsis; /*显示省略符号来代表被修剪的文本。*/
    white-space: nowrap; /*文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。*/
}
```
[详细见demo](./code/text-overflow.html)