# demo-case
一些特殊样式、特殊处理的小demo整合
#### 1. 文字溢出的处理：text-flow属性

我们经常遇到大段文字在显示的时候，希望只显示部分，其他先用省略号（或其他）表示，当点击“查看详情”或者是点击省略号显示剩余的文本。这个时候可以考虑用CSS中的text-flow属性来实现。注意要结合white-space属性一起使用。
```
.text-overflow {
    overflow: hidden;
    text-overflow: ellipsis; /*显示省略符号来代表被修剪的文本。*/
    white-space: nowrap; /*文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。*/
}
```

[详细见demo](./code/text-overflow.html)  


#### 2. `<input type="file">` 触发事件顺序
`<input type="file">` 是用以文件上传的标签。她的显示结果如下：  
<input type="file">   

有时候我们希望在设置只能上传一张图片时，除了在`<input>`中设置 `multiple` 标签属性外，还希望能隐藏上传按钮。就需要了解`<input type="file">`触发时间顺序，以便进行JavaScript代码设置。  

于是我们制作了一个demo，模拟点击选择了一个文件，触发事件的流程是下面这样的：  

>`mousedown` >>> `focus` >>> `mouseup` >>> `click` >>> `blur` >>> `focus` >>> `change`

也就是说：当点击上传按钮，首先触发了鼠标按下事件，然后就是焦点到了 `input` 上面，然后鼠标抬起，触发`click`点击事件，失去焦点以后弹出了文件选择框，选中文件以后触发焦点，最后触发的 `change` 事件。  
如果你没有选择文件的话，直接点击取消的话，就不会触发 `change` 事件。  

所以，如果要实现之前我们提出的 “上传完一张图片后，隐藏按钮”，就可以把对应js代码绑在`change`事件上了。  

[详细见demo，内附base64缩略图生成方法](./code/input-file.html)  

#### 3. `<input>` change事件 
`change` 事件在表单元素`<input>` `<select>` 经常使用。但它的机制并不是简单的`值改变`就触发。  
`change` 触发过程主要如下：

1. 当元素捕获焦点后，系统存储当前值；
2. 当元素失去焦点后，系统判断当前值与之前存储的值是否不相等，若为`true` 就触发`change`事件。  

需要注意的是： 

* __非IE浏览器（fireFox、Chrome、Safari）可以通过敲回车触发   change事件__  
也就是说不需要鼠标点击别处让元素失去焦点。但同样的，也需要存储值和当前值不一致才会触发。  

* __DOM操作改变元素的值，是不会触发  change 事件__  
尤其是，通过JavaScript代码，操作Dom对象改变其value值时，change事件不会触发。  

若希望Dom操作改变元素值，同样去触发change事件的话，可以根据实际情况做一些小技巧。  
网上有一个jq封装的方法，可以做个参考，当然，也并不是广泛适用。[原文地址](http://www.zhixing123.cn/jsp/37858.html)

``` javascript
// 封装插件
(function($) {
     $.fn.update = function(value){
        $(this).each(function(){
            if(value!=this.value){
                this.value = value; 
                this.onchange();
            }
        });
     };
})(jQuery);

// 调用插件
$("input").update("newValue");
```  
