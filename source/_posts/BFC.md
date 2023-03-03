---
title: BFC
date: 2023-02-28 22:03:56
category: [Web前端, CSS]
tags: <span class="label label-primary">BFC</span> <span class="label label-primary">CSS渲染</span>
excerpt: 本文主要介绍了BFC渲染规则以及通过一些实例证明。
---
# 什么是BFC？
在了解BFC之前，先了解一下FC的定义。**FC**（Formatting Context 格式化上下文），FC指定了页面中一块独立区域的渲染规则，这个渲染规则可以理解为内部元素的布局规则。不同的FC有不同的规则定义。

目前网页中几种FC的分类：

- **IFC**（Inline Formatting Context）：行级格式化上下文
- **BFC**（Block Formatting Context）：块格式化上下文
    - 更多关于IFC / BFC的定义参考：[CSS 2.1 Visual formatting model：Section 9.4](https://www.w3.org/TR/CSS2/visuren.html#normal-flow)
- **FFC**（Flex Formatting Context）：自适应格式化上下文
    - 更多关于自适应布局的定义参考 ：[Flexible Box Layout Module Level 1](https://drafts.csswg.org/css-flexbox-1/#flex-formatting-context)
- **GFC**（Grid Formatting Context）：网格布局格式化上下文


# BFC的触发条件
在以下几种情况下，会触发形成BFC：
- 根元素`HTML`
- 浮动元素：`float` 属性不为`none`
- `position`定位：设置为 `absolute` 、`fixed`
- `overflow` 属性：设置为非`visible` 、`clip`的**块**元素
- `display`属性：
    - 设置为 `table`类型：
        - `table`：对应`<table>`
        - `table-caption`：HTML 表格标题默认值
        - `table-row`：对应`<tr>`
        - `table-row-group`：`<tbody>`
        - `table-header-group`：`<thead>`
        - `table-footer-group`：`<tfoot>`
        - `table-cell`：HTML 表格单元格默认值
        - `inline-table`
    - 设置为`inline-block`
    - 设置为`flow-root`
    - 设置为`flex`类型：父元素display属性为`flex` 或者 `inline-flex`，其子元素内部的布局规则为BFC（其子元素本身非`table`类型、`grid`类型、`flex`类型）
    - 设置为`grid`类型：父元素display属性为`gird` 或者 `inline-grid`，其子元素内部的布局规则为BFC（其子元素本身非`table`类型、`grid`类型、`flex`类型）
- `contain` 属性：
    - `layout`
    - `content`
    - `paint`
- *多列容器（暂时没有搞懂）*

# BFC渲染规则
- 渲染隔离：一个BFC是页面上的一个隔离的独立容器，容器里面的子元素的排版布局不会直接影响到外面的元素（可能通过子元素影响父元素容器大小间接影响外部元素布局）。
- 容器大小计算：区域大小由内部元素的边界确定：
    - 即一个BFC容器会包裹所有的子元素块，无论该子元素是否是浮动元素，所以BFC的高度计算会包含`float`元素。
- `margin`重叠：
    - 在同一个BFC中，相邻元素形成的`box`的上下`margin`产生重叠；
    - 但在不同的BFC中，各自的子元素的`margin`不重叠。
- 排布方向：
    - 内部的子元素由上到下依次排布。
    - 内部子元素根据书写方向（`writing-mode`）确定从左开始排布还是从右开始排布。
    - 起始位置的子元素的`margin`紧贴父容器的`border`（只有起始方向的`margin`紧贴）。
- 当前的BFC不与外部的`float box`重叠。

# 实例验证
## 1、容器大小计算
区域大小由内部元素的边界确定，高度计算包含`float`元素
当不给父容器设置高度的情况下，查看父容器的高度是如何计算的：
```html
 <!-- bfc的高度计算会包含float子元素 可以用作清除内部浮动 -->
<!DOCTYPE html>
<html lang="en">
<head>
    <title>BFC</title>
    <style>
        .bfc {
            display: flow-root;
        }
        .container {
            border: 1px solid #939393;
            background: #f2f2f2;
            margin-bottom: 20px;
        }
        .float {
            float: left;
            background-color: #e98f53;
        }
        .child {
            height: 40px;
            width: 40px;
        }  
    </style>
</head>

<body>
	  <!-- 父容器非BFC -->
    <div class="container">
        <div class="float child"></div>
    </div>

		<!-- 父容器是BFC -->
    <div class="bfc container" style="margin-top: 100px">
        <div class="float child"></div>
    </div>
</body>
</html>
```

![](1.png)

## 2、margin重叠
同一个BFC中相邻的元素上下`margin`重叠
当给子元素设置

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>BFC</title>
    <style>
        .bfc {
            display: flow-root;
        }
        .container {
            border: 1px solid #939393;
            background: #f2f2f2;
        }
       .bfc-child {
            height: 40px;
            margin: 30px 0;
            background-color: #ccc;
       }
    </style>
</head>

<body>
	<div class="bfc container">
        <div class="bfc-child"></div>
    </div>
    <div class="bfc container">
        <div class="bfc-child"></div>
        <div class="bfc-child"></div>
    </div>
</body>
</html>
```

![](2.png)

相反，当父容器非BFC时，所有子元素（无论是否是平级）都会发生上下`margin`重叠。
将上面父容器的display属性改为`contents` ：

```css
.bfc{
	display: contents;
}
```

![](3.png)

> contents
> These elements don't produce a specific box by themselves. They are replaced by their pseudo-box and their child boxes. 
> 
> 当设置元素为content时，该元素不会生成自己的box，而是会被子元素生成的box取代。
> 

会发现此时处于两个父级下的子元素，上下`margin`也会发生重叠。此时因为父容器不是BFC，没有渲染隔离，所有子元素生成的盒子被平铺到了最外层。

## 3、当前的BFC不与外部的`float box`重叠
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BFC</title>
    <style>
	     .bfc {
            display: flow-root;
        }
        .float {
            float: left;
            background-color: #e98f53;
            height: 40px;
            width: 40px;
        }
        .aside {
            width: 200px;
            height: 200px;
            background-color: #696fa7;
        }
    </style>
</head>

<body>
    <!-- 规则三：bfc区域不和浮动元素重叠 -->
    <div class="float"></div>
    <div class="aside">该元素非BFC，则该元素和小黄块重叠</div>

    <div class="float"></div>
    <div class="aside bfc">该元素BFC，则该元素和小黄块不重叠</div>

</body>

</html>

```

![](4.png)

# BFC的作用

利用BFC的渲染规则，可以实现以下几个功能：
- 自适应两栏布局
- 阻止元素被浮动元素覆盖
- 清除内部浮动
- 阻止`margin`重叠：分属于不同的BFC时不发生重叠

后面三条在实验中以及实现过了，但是针对第一条，如何利用BFC实现两栏布局呢？

利用2个特性：
1. 块级元素`width:auto` 时，会自动拉伸至父元素左右边界处（横向占满）。
2. BFC渲染规则：当前的BFC不与外部的`float box`重叠。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BFC</title>
    <style>
        .bfc {
            display: flow-root;
        }
				.container {
            border: 1px solid #939393;
            background: #f2f2f2;
            margin-bottom: 20px;
        }
        .main{
            height: 300px;
            background: #ddd;
        }
        .float-aside{
            width: 100px;
            height: 150px;
            float: left;
            background: #757575;
        }
    </style>
</head>

<body>
    <!-- 利用bfc区域不和浮动元素重叠的特性，实现自适应两栏布局 -->
    <div class="bfc container">
        <div class="float-aside"></div>
        <div class="bfc main">设置float的宽度，然后给相邻的bfc元素不设置宽度，块级元素默认宽度为auto，那么该bfc元素会自动延伸到父元素的最右端</div>
    </div>
</body>
</html>
```

![](5.png)

# 参考
[[布局概念] 关于CSS-BFC深入理解](https://juejin.cn/post/6844903476774830094)
[CSS Display Module Level 4](https://drafts.csswg.org/css-display/#formatting-context)

