---
title: meetOrSlice算法
date: 2023-03-03 15:11:28
category: [Web前端, SVG]
excerpt: 本文重点是实现preserveAspectRatio中<meetOrSlice>参数的效果，也是background-size中值为cover，contain属性效果。
tags: <span class="label label-primary">正则表达式</span>
---

# 一、前言

本文重点是实现`preserveAspectRatio`中`<meetOrSlice>`参数的效果，也是`background-size`中值为`cover`，`contain`属性效果。在实现之前，简单介绍一下**SVG**中的`viewport`, `viewBox`和`preserveAspectRatio`的关系。

## 画布

 当我们创建SVG标签时，实际上是创建了一个**隐形的无限延伸**的画布。



## 视窗（viewport）

 画布是无限延伸的，但是人的视野是有限的，我们需要设置一个固定的区域，然后在这个固定的区域去绘制图形，使其可见。所以我们通过给SVG设置`width`和`height`属性（或者通过css设置宽高）设置一个可见区域，这个区域就是**viewport**，也就是视窗。这个区域是初始坐标系。

 在不做任何坐标系转换 (transform) 的情况下，我们绘制一个SVG图形，实际是绘制在画布上，但是是以**viewport**的坐标系为参考坐标系绘制的。可以理解为画布是绘制实体，视窗是用来确定具体绘制的位置和尺寸的。


## viewBox


 什么是**viewBox**呢？

 可以理解为我们手里有一个任意尺寸的方形框，即**viewBox**，这个框可以在视窗的区域内任意位置游走。我们通过设置**viewBox**的属性值，可以指定这个框的尺寸（width, height），以及具体游走在视窗的那个位置（x, y）。

 在框内的画面就是我们最终看到的画面，我们将框内的区域“裁剪”出来，然后通过缩放填充整个视窗。而具体的缩放规则就要看**preserveAspectRatio**属性设置的值。




## preserveAspectRatio

 我们通过**viewBox**裁剪了一个区域，然后将这个区域缩放填充整个视窗。为了便于理解，我们将通过viewBox裁剪出来的区域称为**content**，将视窗称为**box**。

 为了使**content**在缩放过程不变形，我们需要保持**content**的宽高比，如果**content**和**box**的宽高比相等，则**content**通过缩放可以刚好填满整个**box**。但是如果宽高比不相等，那应该如何填充？

 **preserveAspectRatio**就是为了解决这个问题而产生的。**preserveAspectRatio**中有两个参数`<align>`和`<meetOrSlice>`，一个值决定**content**按照什么规则缩放，一个值决定缩放后的**content**与**box**的对齐方式。

 其中`<meetOrSlice>`属性值的效果和`background-size`中`cover`和`contain` 类似。




关于更详细的SVG坐标系的解释，参考这两篇文章：

[理解SVG坐标系统和变换： 建立新视窗](<https://www.w3cplus.com/html5/nesting-svgs.html>)

[理解SVG坐标系和变换：视窗,viewBox和preserveAspectRatio](<https://www.w3cplus.com/html5/svg-coordinate-systems.html>)

接下来就详细介绍一下`<meetOrSlice>`是如何控制填充效果的。



# 二、meetOrSlice定义

为了行文方便，仍然用**content**和**box**分别代指用于填充的矩形图和被填充的盒子。因为本文重点在于探索和实现**preserveAspectRatio**的`<meetOrSlice>`效果，在这里就不对`<align>`参数展开介绍了。



`<meetOrSlice>`参数有两个值：`meet`和`slice`，其中`meet`类似于`background-size`中的`contain`，`slice`类似于`background-size`中的`cover`。

在[MDN preserveAspectRatio](<https://developer.mozilla.org/zh-CN/docs/Web/SVG/Attribute/preserveAspectRatio>)对这两个属性值是这样描述的：



> - meet (默认值)  图形将缩放到:
>   - 宽高比将会被保留
>   - 整个SVG的viewbox在视图范围内是可见的
>   - 尽可能的放大SVG的viewbox，同时仍然满足其他的条件。
>
> 在这种情况下，如果图形的宽高比和视图窗口不匹配，则某些视图将会超出viewbox范围（即SVG的viewbox视图将会比可视窗口小）。
>
> - slice 图形将缩放到:
>   - 宽高比将会被保留
>   - 整个视图窗口将覆盖viewbox
>   - SVG的viewbox属性将会被尽可能的缩小，但是仍然符合其他标准。
>
> 在这种情况下，如果SVG的viewbox宽高比与可视区域不匹配，则viewbox的某些区域将会延伸到视图窗口外部（即SVG的viewbox将会比可视窗口大）。

简单概括就是两句话，在保持**content**宽高比不变的情况下：

`meet / contain `：缩放，使**box**包含(contain)完整的**content**，**box**内部可能产生空白。重点：不产生“越界”现象

`slice / cover`：缩放，使**content**覆盖(cover)**box**全部区域，**content**可能会超出**box**区域。重点：不产生空白区域



# 三、理解meetOrSlice的填充规则

 在了解了这个两个属性值的含义之后，我们再来探究这两个值的具体计算规则。**box**和**content**都是矩形，我们将矩形分为三类：正方形、竖向矩形、横向矩形：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c1618d33f9c79~tplv-t2oaga2asx-image.image)
**box**和**content**分别有可能是其中的任意一类，通过排列组合可以得到9种对应关系（相等，同向，异向）：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c161e53a40906~tplv-t2oaga2asx-image.image)

为了更精确的对比所有尺寸得到的结果，我们分别设置三种类型的**box**尺寸，以及三种类型的**content**尺寸，如下：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c16219b143378~tplv-t2oaga2asx-image.image)


在**SVG**中，设置`meet`和`slice`，得到的结果如下（SVG缩放中会影响到矩形的边框宽度，所以同样的边框宽度因为缩放比例不同会导致最终的视觉宽度不同）：
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c162390e34466~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c1625c3a49f49~tplv-t2oaga2asx-image.image)



根据定义和实验结果，我们可以发现：

- `meet`模式下，为了使 **content**最大程度的被完整包含在**box**内部，总是**content**的**长边**与**box**对应边对齐。

- `slice`模式下，为了使**box**被填满，总是**content**的**短边**与**box**对应边对齐。

但这两点并不能包含全部情况，我们看👇例子：


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c1632ee3c06e3~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c16354a9da897~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c16379452d7e0~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c1639f03b62ff~tplv-t2oaga2asx-image.image)



从上面的4个例子中可以发现，当**box**和**content**都是同向的矩形（均为横向或者竖向）时，`meet`情况下，还需要继续判断短边的长度；而在`slice`的情况下还需要继续判断长边的长度。

所以我们可以这样描述：

- `meet`：总是比较两者的**长边**：

  - 长边同边（同边：都是横向或者竖向矩形）：假设都为横向矩形，根据宽高比计算在**同一个宽度**下两者的高度，判断哪个的高度更高（反之同理）：
    - 如果`contentHeight > boxHeight`（此时**box**更“扁”）：为了使**content**被完整包含在**box**里，需要让**content**的高等于**box**的高（对齐高）
    - 如果`boxHeight > contentHeight`（此时**content**更“扁”）：使**content**的宽等于**box**的宽（对齐宽）

  - 长边异边（异边：一个为横向另一个为竖向）：假设**content**为横向，长边为宽，**box**为竖向，长边为高（可以理解为content更扁一点），则使**content**的宽等于**box**的宽（对齐宽），反之同理。



![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c17e213819216~tplv-t2oaga2asx-image.image)

- `slice`：总是比较两者的**短边**：
  - 短边同边（同边：同上描述）：假设都为横向矩形，根据宽高比计算在**同一个宽度**下两者的高度，判断哪个的高度更高：
    - 如果`contentHeight > boxHeight`（此时box更“扁”） ：使**content**的宽等于**box**的宽（对齐宽）
    - 如果`boxHeight > contentHeight`（此时content更“扁”）：使**content**的高等于**box**的高（对齐高）
  - 短边异边（异边：同上描述）：假设**content**为横向，短边为高，**box**为竖向，短边为宽，（content更扁），使**content**的高等于**box**的高（对齐高），反之同理。
  
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/29/171c18c514237275~tplv-t2oaga2asx-image.image)


现在，我们已经知道`meet`和`slice`的缩放规律，根据图，我们可以进一步归纳逻辑。在这里，根据矩形形状的特点，使用“扁”作为统一标准：同宽情况下，高度越小，越扁。

在保持**content**宽高比缩放的情况下，比较**content**和**box**的“扁”度：

- meet
  - box > content  => 对齐高：**content**的宽高同时乘以一个值使**content**的高等于**box**高
  - box < content => 对齐宽：**content**的宽高同时乘以一个值使**content**的宽等于**box**宽

- slice
  - box > content  => 对齐宽：同上
  - box < content =>对齐高： 同上

伪代码可以这样描述：

```
if(type == 'meet'){
  if(box_扁 > content_扁){
   content_高 * scale = box_高;
   content_宽 * scale = new_content_宽;
  }else{
	content_宽 * scale = box_宽;
	content_高 * scale = new_content_高;
  }
}else if(type == 'slice'){
 if(box_扁 > content_扁){
	content_宽 * scale = box_宽;
	content_高 * scale = new_content_高;
  }else{
	content_高 * scale = box_高;
   	content_宽 * scale = new_content_宽;
  }
}
```



# 四、实现meetOrSlice方法

根据伪代码的描述，为了真正实现这个算法，我们需要：

- 得到一个矩形的“**扁**”度

- 得到**scale**值

其实上面两点非常容易获取：

- 如果一个矩形越扁，意味着宽高比越大，所以可以通过宽高比 `width / height` 来获取“扁”度。

- 而**scale**，在伪代码中其实已经能发现scale的计算方法（假设对齐宽）：

```
contentW * scale = boxW;
scale = boxW / contentW;


所以缩放后的content宽高为：
newContentW = scale * contentW;
newContentH = scale * contentH;
```



由此，我们就可以写出`meet`和`slice`方法了：

```javascript
function meetOrSlice(type, boxW, boxH, contentW, contentH){
    let boxRadio = boxW / boxH,
        contentRadio = contentW / contentH,
        scaleW = (boxW / contentW) || 1,
        scaleH = (boxH / contentH) || 1,
        scale = 1;
    if(type == 'meet'){
        scale = boxRadio >= contentRadio ? scaleH : scaleW;
    }else if(type == 'slice'){
        scale = boxRadio >= contentRadio ? scaleW : scaleH;
    }
    
    return {
        w: scale * contentW,
        h: scale * contentH
    }
}
```


最后和SVG对比一下效果，其中关于边框的两个问题： 

- svg中边框宽度不一致：是因为SVG缩放中会连同边框一起缩放 
- svg中部分 部分边框被裁切：是因为svg内部矩形的x,y定位，是以边框的中线为标准计算的，所以当坐标点为`(0,0)`时，会有一半的边框超出svg的窗口范围，导致被截断。


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c1656567c3728~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c165994da0a5e~tplv-t2oaga2asx-image.image)
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/28/171c165af0998dc9~tplv-t2oaga2asx-image.image)



[点击查看源码](https://github.com/kkkkira/demo/blob/master/meetOrSlice/index.html)

完结，撒花🎉。



### 参考

[理解SVG坐标系统和变换： 建立新视窗](<https://www.w3cplus.com/html5/nesting-svgs.html>)

[理解SVG坐标系和变换：视窗,viewBox和preserveAspectRatio](<https://www.w3cplus.com/html5/svg-coordinate-systems.html>)

[MDN preserveAspectRatio](<https://developer.mozilla.org/zh-CN/docs/Web/SVG/Attribute/preserveAspectRatio>)