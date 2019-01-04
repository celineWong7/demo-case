# demo-case
一些特殊样式、特殊处理的小demo整合
## 1. 文字溢出的处理：text-flow属性

我们经常遇到大段文字在显示的时候，希望只显示部分，其他先用省略号（或其他）表示，当点击“查看详情”或者是点击省略号显示剩余的文本。这个时候可以考虑用CSS中的text-flow属性来实现。注意要结合white-space属性一起使用。
```
.text-overflow {
    overflow: hidden;
    text-overflow: ellipsis; /*显示省略符号来代表被修剪的文本。*/
    white-space: nowrap; /*文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。*/
}
```

[详细见demo](./code/demo1-text-overflow.html)  


## 2. `<input type="file">` 触发事件顺序
`<input type="file">` 是用以文件上传的标签。她的显示结果如下：  
<input type="file">   

有时候我们希望在设置只能上传一张图片时，除了在`<input>`中设置 `multiple` 标签属性外，还希望能隐藏上传按钮。就需要了解`<input type="file">`触发时间顺序，以便进行JavaScript代码设置。  

于是我们制作了一个demo，模拟点击选择了一个文件，触发事件的流程是下面这样的：  

>`mousedown` >>> `focus` >>> `mouseup` >>> `click` >>> `blur` >>> `focus` >>> `change`

也就是说：当点击上传按钮，首先触发了鼠标按下事件，然后就是焦点到了 `input` 上面，然后鼠标抬起，触发`click`点击事件，失去焦点以后弹出了文件选择框，选中文件以后触发焦点，最后触发的 `change` 事件。  
如果你没有选择文件的话，直接点击取消的话，就不会触发 `change` 事件。  

所以，如果要实现之前我们提出的 “上传完一张图片后，隐藏按钮”，就可以把对应js代码绑在`change`事件上了。  

[详细见demo，内附base64缩略图生成方法](./code/demo2-input-file.html)  

## 3. `<input>` change事件 
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


## 4. for循环中出现异步函数，回调引用的循环变量不能保持当前值。

直接用一个具体事例说明：
```javascript
    var ary = ['one', 'two', 'three'];

    for(var i=0; i<ary.length; i++ ) {
        // 延时器里的函数变成了异步函数
        setTimeout(function(){
            document.write(i + ' : ' + ary[i] + '<br>');
        },500);
    }
```

输出结果：
```
3 : undefined
3 : undefined
3 : undefined
```
for循环是同步任务，当延时器要执行document.write()的时候，for循环早就结束了（当i=3，for循环结束），所以i都等于3，对应的数组元素都为undefined的。
如果希望得到i=1,2,3这种方式，可以采取以下两种方式。

1. 解决方法1
```javascript
    /** 方法1 自执行函数 */
    for(var i=0; i<ary.length; i++ ) {
        // 通过自执行函数传参（匿名函数），这样就形成了不受外界变量影响的局部作用域
        (function(i){
            // 延时器里的函数变成了异步函数
            setTimeout(function(){
                document.write(i + ' : ' + ary[i] + '<br>');
            },500);

        })(i)
    }
```

输出结果：
```
0 : one
1 : two
2 : three
```

2. 解决方法2
```javascript
    /** 方法2 jq的each方法代替for循环（需要引入jQuery库文件） */
    $.each(ary, function(key, value){
        // 延时器里的函数变成了异步函数
        setTimeout(function(){
            document.write(key + ' : ' + value + '<br>');
        },500);
    });
```

输出结果：
```
0 : one
1 : two
2 : three
```

[详细见demo](./code/demo3-for-asych.html)  


## 5. html中无法展示回车\r\n的问题
有时候后端传递回来的字符串回车时用`\n`表示，但在浏览器被当做空白符处理（除非是放置在`<pre></pre>`标签里头）。
可以考虑用正则替换`\n`为`<br/>`。
```
var str = "aaaa\nbbbb\nccc";
str.replace(/\n/g, "<br/>");
```


## 6. Number、parseFloat、parseInt的联系与区别
具体可以查看此篇[JavaScript 里Number、parseFloat、parseInt的联系与区别](https://www.jianshu.com/p/55a54abdaef8)。
此处只给出比较表格和demo。  

![三种方法的比较](../image/Number-parseInt.png)

[详细见demo](./code/demo6-toNumber.html) 

## 7. 自执行匿名函数
具体可以查看此篇[浅谈自执行函数（立即调用的函数表达式）](https://www.jianshu.com/p/c64bfbcd34c3)。  
此处只给出常见的应用方式。（可以结合 4. 内容查看）
```
// 方式一
for( var i=0;i<3;i++){
    (function(lockedIndex){
        setTimeout(function(){
            console.log(lockedIndex);
        }
        ,300);
    })(i);
}


// 方式二
for( var i=0;i<3;i++){
    setTimeout((function(lockedInIndex){
        console.log(lockedInIndex);// 输出 "1,2,3"
    })(i)
    ,300);
}

```
[详细见demo](./code/demo7.html) 