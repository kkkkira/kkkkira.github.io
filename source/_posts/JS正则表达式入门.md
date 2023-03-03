---
title: JS正则表达式入门
date: 2023-03-03 15:09:17
category: [Web前端, javascript, 正则表达式]
tags: <span class="label label-primary">正则表达式</span>
excerpt: 正则表达式就是对字符串操作的一种逻辑公式，就像数学公式一样，通过按照一定的规则，组合使用`+ - * / （）=` 以及数字或者变量，形成一个表达式。
---

## 简单理解正则表达式

正则表达式是用于匹配字符串中字符组合的模式，可以这样定义：

> 正则表达式是对字符串（包括普通字符（例如，a 到 z 之间的字母）和特殊字符（称为“元字符”））操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。正则表达式是一种文本模式，该模式描述在搜索文本时要匹配的一个或多个字符串。
>
> 胡军伟, 秦奕青, 张伟. 正则表达式在Web信息抽取中的应用[J]. 北京信息科技大学学报(自然科学版), 2011, 26(6):86-89.

可以简单理解为，正则表达式就是对字符串操作的一种逻辑公式，就像数学公式一样，通过按照一定的规则，组合使用`+ - * / （）=` 以及数字或者变量，形成一个表达式。正则表达式也是使用一些具有特殊含义的**特定符号**，来抽象表达符合某种规则的字符串。这种特定符号，我们称之为**元字符**。元字符按功能又可以分为以下几种类型：

- 字符
- 位置
- 量词
- 分支
- 分组
- 引用

通过对不同类型的元字符进行任意组合，就可以生成一个模式，使用这个模式，就可以在任意字符串中匹配你想要的内容。在js中，我们一般使用 `/表达式/ `的字面量来表示这是一个正则表达式。



## 字符类型

在一个字符串中，如果我们想要要匹配某个或者某种类型的字符，我们可以选择**精确匹配**和**模糊匹配**。

#### 模糊匹配

模糊匹配就是一个**元字符**可以表示某一类字符集。

下表列出了一些常用的元字符集：

| 字符     | 定义                         | 描述                                                         |
| -------- | ---------------------------- | ------------------------------------------------------------ |
| `.`      | 通用符                       | 匹配除换行符（`\n`、`\r`）之外的任何单个字符                 |
| `\s`     | 空白符（space）              | 匹配任何**空白字符**，包括**空格**、**换页符**、**换行符**、**回车符**、**制表符**、**垂直制表符**<br />等价于 `[ \f\n\r\t\v\u00a0\u1680\u180e\u2000-\u200a\u2028\u2029\u202f\u205f\u3000\ufeff]`<br />⚠️ 这里有一个空格符号<br />⚠️ 记忆：空格符（十进制、八进制、十六进制） +  表中的特殊符号（十进制、八进制、十六进制） |
| `\S`     | 非空白符                     | 匹配任何**非**空白字符<br />等价于`[^ \f\n\r\t\v\u00a0\u1680\u180e\u2000-\u200a\u2028\u2029\u202f\u205f\u3000\ufeff]`（这里的`^`表示**非**的意思） |
| `\d`     | 数字符（digital）            | 匹配一个数字。等价于`[0-9]`                                  |
| `\D`     | 非数字符                     | 匹配一个非数字符。等价于`[^0-9]`                             |
| `\w`     | 字母、数字、下划线符（word） | 匹配一个单字字符（字母、数字或者下划线）。<br />等价于 `[A-Za-z0-9_]`。 |
| `\W`     | 非（字母、数字、下划线）符   | 匹配一个非单字字符。<br />等价于 `[^A-Za-z0-9_]`。           |
| `\f`     | 换页符（特殊符号）           |                                                              |
| `\n`     | 换行符（特殊符号）           |                                                              |
| `\r`     | 回车符（特殊符号）           |                                                              |
| `\t`     | 制表符（特殊符号）           |                                                              |
| `\v`     | 垂直制表符（特殊符号）       |                                                              |
| `[xyz]`  | 自定义字符集                 | 你可以在字符集中任意组合元字符和普通字符，形成一个自定义字符集，从而使字符串去匹配方括号中的任意字符。<br />可以使用 `-` 破折号代表范围<br />🌰<br />`[xyz]`：可以匹配`x` `y` `z` 三个字符中的任意一个<br />`[0-9a-z]`：等价于`[0123456789zbcdefghijklmnopqrstuvwxyz]` 表示匹配任意一个数字或者小写字母<br />`[\dAa-z]` ：表示可以匹配任意一个数字（`\d`）或者小写字母(`a-z`)以及大写字母`A` |
| `[^xyz]` | 自定义（排除）字符集         | 在方括号内部的开头加上`^`，表示匹配任意一个**不是**自定义字符集内部的字符。如果在中间加上非呢？<br />🌰<br />`[^xyz]`：匹配**排除**`x` `y` `z` 以外的任意一个字符<br />`[^\dAa-z]` ：表示可以匹配任意一个 **不是** 数字（`\d`） 且**不是**小写字母(`a-z`)且**不是**大写字母`A`的字符 |

#### 精确匹配

除**元字符**外，其他任意一个字符，只匹配字符本身。例如`/a/`，只能匹配字符串`abc`中的`a`。

 如果想要匹配元字符**本身**，使用转义符号`\`进行转义。

🌰 想要匹配 `.` ，则写成`\.`

🌰 想要匹配`+`（量词类型元字符），则写成`\+`



#### 单字符匹配

⚠️ 无论是模糊匹配还是精确匹配，在**不加量词**的情况下，一个**字符符号**（元字符或者普通符号）只能匹配单个字符。我们可以把正则里面的符号理解为占位符，一个萝卜一个坑。所以只要当前这个符号匹配到了字符，坑就被占了，下一个字符就需要有新的符号去匹配。所以我们如果要匹配多个字符，只要将多个**字符符号**组合在一起，形成一个子串模式，就能匹配一个满足条件的连续字符串。


##### 匹配数字

```javascript
let str = 'the id is: 123';
let reg = /\d/;

console.log(str.match(reg)); 
// [ '1', index: 11, input: 'the id is: 123', groups: undefined ]
// match方法返回一个数组，其中数组的第一个元素表示匹配到的内容。
// 可以看到每次只匹配一个字符
```



##### 替换空白符号

```javascript
let str = 'the space is: \n,\f,\r,\t,\v';
let reg = /\s/g;	// 将整个字符串中的空白符都替换为#

console.log(str.replace(reg, '#'));
// 一次只会匹配成功一个，replace方法的说明在下面
//the#space#is:##,#,#,#,#
```



##### 删除所有非单字符

```javascript
let str = '删除当前行中非数字(delete not number)123，(not word)非英文字母和非下划线_的符号！';
let reg = /\W/g;

console.log(str.replace(reg, ''));	
//deletenotnumber123notword_
```



##### 自定义字符集

```javascript
let str = '在字符串中只留下字符集中的字符: 数字123，通配符. 普通英文字母wwwww';

let reg1 = /[^\.数w]/g;
console.log(str.replace(reg1, ''));
// 数.wwwww

let reg2 = /[\.数w]/g;
console.log(str.replace(reg1, ''));
//在字符串中只留下字符集中的字符: 字123，通配符 普通英文字母
```



## 位置类型

什么是位置，什么又是位置字符呢？

其实在一个连续的字符串中，相邻字符之间是存在一个位置的，我们可以把这个位置理解为空字符。如图所示（图源网络）：


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/5/1731e6ec5794d5c5~tplv-t2oaga2asx-image.image)

在正则中，我们除了可以匹配具体字符以外，还可以使用元字符来表示具体要匹配哪个位置。

在js中，表示位置的元字符分为4类：

| 位置     | 相关元字符                                   |
| -------- | -------------------------------------------- |
| 开始     | `^`                                          |
| 结尾     | `$`                                          |
| 单词边界 | `\b` `\B`                                    |
| 断言     | `(?=)`<br />`(?!)`<br />`(?<=)`<br />`(?<!)` |



#### 开始

元字符`^`既可以表示位置，也可以代表非。

- 表示位置时：这个元字符写在整个正则表达式的最开始。
- 表示非时：写在方括号`[]`的最开始。

当表示开始位置，代表当前正则表达式**必须**从字符串的**最开始**进行匹配。如果不存在这个元字符，只要字符串中任意一个子串满足正则表达式即可。

```javascript
let reg = /^\d/;	// 表示匹配到的字符串必须以数字开头
let str1 = "abcdefg123";
let str2 = "123abcd";

console.log(reg.test(str1)) // false
console.log(reg.test(str2)) // true
```



我们通常情况下都是对一个连续的单行字符串进行匹配，`^` 表示整个字符串的最开始。如果想要匹配多行文本中每行的开头，需要满足两个条件：

1. 当前字符串需要使用换行符`\n`分割形成多行文本，`^`表示换行符**后**紧跟的位置
2. 需要将正则的多行标志`multiple`置为`true`（之后说明）

```javascript
let reg = /^\d+/mg;	// m表示开启多行匹配。匹配当前多行文本中所有以数字开头的行，并获取开头的数字内容
let str = '123ad\n0abc123\nabc123';
let a = str.match(reg);
console.log(a);

//[ '123', '0' ]
```



#### 结束

元字符 `$` 表示结束位置。匹配输入的结束。同 `^` 一样，如果多行标志被设置为 `true`，那么也匹配换行符`\n`**前**紧贴的位置。

```javascript
let reg = /\d$/;	// 表示匹配到的字符串必须以数字结尾
let str1 = "abcdefg123";
let str2 = "123abcd";

console.log(reg.test(str1)) // true
console.log(reg.test(str2)) // false
```



```javascript
let reg = /\d+$/mg;	// m表示开启多行匹配。匹配当前多行文本中所有以数字开头的行，并获取开头的数字内容
let str = '123ad\n0abc2\nabc3';
let a = str.match(reg);
console.log(a);

//[ '2', '3' ]
```



#### 单词边界

其实在这里，`\b` 表示单词边界的位置，`\B`表示**非单词边界**的位置。

那什么是单词呢？我们回到之前的字符类型的元字符表可以看到，`\w`其实就是word，连续的word形成了words，就是代表了这里的单词。我们已经知道`\w`等价于`[A-Za-z0-9_]`，所以这里的单词就是指带了这些字符。

那边界的定义又是什么？

我们已知，每两个相邻字符之间，都存在一个位置，如果相邻两个字符都属于`\w`，那这两个相邻字符之间的位置就叫**非单词边界`\B`**；如果两个相邻字符**只有一个**字符属于`\w`，或者**两个都不属于**`\w`，那它们之间的位置就叫做**单词边界`\b`**。 （`b`可以理解为`break`）

需要注意的一点，字符串的开始和结束，都算作单词边界。可以理解为在字符串的开始和结束，存在一个空字符`""` ，那么无论相邻的字符是否属于`\w`，都存在一个空字符`""`不属于`\w`。


```javascript
let breakReg = /\b/g,
    notBreakReg = /\B/g;
let str = "word_break.mp3";
let bStr = str.replace(breakReg, "|");
let Bstr = str.replace(notBreakReg, "|");
console.log("bStr is: ", bStr);
console.log("Bstr is: ", Bstr);


// bStr is:  |word_break|.|mp3|
// Bstr is:  w|o|r|d|_|b|r|e|a|k.m|p|3
```



#### 断言

> 零宽断言正如它的名字一样，是一种零宽度的匹配，它匹配到的内容不会保存到匹配结果中去，最终匹配结果只是一个位置而已。
> 作用是给指定位置添加一个限定条件，用来规定此位置之前或者之后的字符必须满足限定条件才能使正则中的字表达式匹配成功。
>
> ​									 				     ——源自博客《正则表达式零宽断言详解》

听起来断言很复杂，其实道理很简单。所谓断言，其实就是我们的正则表达式在**匹配位置**的时候，必须要满足一定的条件才行。这里的条件，就是一个子表达式，其中断言又分为：

- 先行：
  - 正向：`(?=子表达式)`
  - 负向：`(?!子表达式)`
- 后行：
  - 正向：`(?<=子表达式)`
  - 负向：`(?<!子表达式)`

在这里，先行和后行为相反组，表示匹配的位置在前还是在后；正向和负向为相反组，表示是否满足条件。组合起来的意思就是：

- 正向先行`(?=)`：匹配**满足**子表达式的字符串**前面**的位置
- 正向后行`(?<=)`：匹配**满足**子表达式的字符串**后面**的位置
- 负向先行`(?!)`：匹配**不满足**子表达式的字符串**前面**的位置
- 负向后行`(?<!)`：匹配**不满足**子表达式的字符串**后面**的位置


简单记忆：

- 正向：满足
- 负向：不满足
- 先行：前面
- 后行：后面



##### 正向（先/后）行

```javascript
let str = '在js前面插入===,后面插入---: 前端基础三件套html,css,js; node, npm, react或者vue是新的三件套';

let reg1 = /(?=js)/g;
console.log(str.replace(reg1, '==='));
// 在===js前面插入===,后面插入---: 前端基础三件套html,css,===js; node, npm, react或者vue是新的三件套


let reg2 = /(?<=js)/g;
console.log(str.replace(reg2, '---'));
// 在js---前面插入===,后面插入---: 前端基础三件套html,css,js---; node, npm, react或者vue是新的三件套
```


##### 负向（先/后）行

```javascript
let str = 'the number is 0000'; 
// 在所有非字母和非空白符的字符前插入 ===，后面插入---
// 实际上在这个字符串中会有三个字符会匹配成功：数字字符、字符串的开始空字符、结束空字符。

let reg1 = /(?![a-zA-Z\s])/g;
console.log(str.replace(reg1, '==='));
// the number is ===0===0===0===0===
// 最后一个===实际上是匹配到了结束空字符''，在结束空字符前插入了===

let reg2 = /(?<![a-zA-Z\s])/g;
console.log(str.replace(reg2, '---'));
// ---the number is 0---0---0---0---
// 第一个---实际上是匹配到了开始空字符''，在开始空字符后插入了---
```





## 量词类型

根据前面的了解，我们知道 一个符号只能匹配一个字符，如果要匹配多个字符，就需要组合多个符号（包括元字符和普通字符）来进行匹配。但是如果要匹配一个连续重复的字符串`aaaaaaaaa`，按照前面的逻辑，我们的正则表达式要写出 `/aaaaaaaaa/` 才行。有没有更简便的方法呢？

答案是有的。

在正则中，存在这种量词类型的元字符，来简化表达式本身。例如上面的正则表达式我们可以写成`/a{9}/`，表示当前满足条件，能匹配成功的子串包含9个连续的a。

量词类型的元字符用来表示重复次数，具体重复的对象，是前面紧跟的子模式。

其中，量词元字符包括：

| 字符     | 描述           |
| -------- | -------------- |
| `*`      | 重复0到n次     |
| `+`      | 重复1到n次     |
| `?`      | 出现0次或者1次 |
| `{n}`    | 重复n次        |
| `{n,}`   | 至少重复n次    |
| `{n, m}` | 重复n到m次     |



例如：

```javascript
let str = 'the number is 0000123122, phone number is 057-9981013';

let reg1 = /\d+/g;
let reg2 = /[num]{3}/g;
let reg3 = /0{2,4}/g;
let reg4 = /,?/;

console.log(str.match(reg1))
// [ '0000123122', '057', '9981013' ]

console.log(str.match(reg2))
// [ 'num', 'num' ]

console.log(str.match(reg3))
// [ '0000' ]

console.log(reg4.test(str))
// true
```



## 分组类型

我们已知如何匹配一段由一个字符重复n次构成的字符串，例如 `aaaaaa` 。假如现在我们的字符串是`123xyzxyzxyz1axyzxyz`，很明显，这串字符中也存在重复的子串，但是并不满足单字符重复的特点，我们无法使用已经学到的东西来写出正则表达式。这个时候，就是分组类型的元字符上场的时候了。

分组由圆括号 `()` 来表示。括号内部是子表达式，表示一个子串的匹配模式。说白了，分组就是把一些特殊的单字符组合看作一个单元，我们在匹配时，遇到这样的单元就当做一个整体来处理。具体是哪些特殊字符的组合，就可以任意定义，然后写成子模式。例如上面的字符串，我们把`xyz`看作一个单元，想要匹配出`xyz`，就可以这样写：

```javascript
let str = "123xyzxyzxyz1axyzxyz";
let reg = /(xyz)/g;
let match = str.match(reg);

// [ 'xyz', 'xyz', 'xyz', 'xyz', 'xyz' ]
```



如果把连续的两个甚至多个`xyz`看作一个单元来匹配，只需要需要在分组后添加量词即可。

```javascript
let str = "123xyzxyzxyz1axyzxyz";
let reg = /(xyz)+/g;
let match = str.match(reg);

// [ 'xyzxyzxyz', 'xyzxyz' ]
```



在分组类型的元字符中，有三种类型：

| **捕获分组**     | **`（子表达式）`**    |
| ---------------- | --------------------- |
| **命名捕获分组** | **(?<name>子表达式)** |
| **非捕获分组**   | **`(?:子表达式)`**    |



##### 捕获与非捕获

其区别就在于，当我们使用捕获分组的时候，每个子表达式匹配到的内容都会被分别记录下来，后面我们可以通过相应的api或者是引用类型的元字符获取到不同分组的匹配项；而非捕获分组就是不记录这个匹配项。例如：

```javascript
let reg = /(\d+)(?:[a-z]+)/;	
let str = "123abcd";

console.log(str.match(reg))
// [ '123abcd', '123', index: 0, input: '123abcd', groups: undefined ]
// 在这里，123abcd是整个正则匹配到的内容，而123则是我们捕获分组匹配到的内容
// 可以看到，此时非捕获分组匹配到的内容并没有被保存下来，我们无法单独获取
```



##### 匿名捕获与命名捕获

其中捕获分组又分为命名和匿名分组，匿名分组则使用索引值来获取，而命名分组除了可以使用索引值访问以外，还可以通过指定的属性访问获取。

```javascript
let reg = /(\d+)(?<char>[a-z]+)/;
let str = "123abcd";

console.log(str.match(reg))
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/5/1731e6ccb87320ec~tplv-t2oaga2asx-image.image)


从结果中我们可以看到，使用命名捕获分组匹配到的分组，不但可以通过索引值来获取，还可以通过`groups.char`获取。

## 引用类型

引用，其实就是对捕获分组匹配到的内容的引用。

在两个地方可以使用引用：

- 在表达式本式中
- 在相关的函数中

##### 表达式中的引用

在表达式中，有两种方法来获取引用：

| 元字符     | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| `\n`       | 匿名捕获分组的引用。它返回第n个子捕获匹配到的具体的内容(捕获的数目以左括号计数，即不论是否嵌套分组，命名捕获还是匿名捕获，都是按照左括号来计算，第一个左括号捕获的分组就是`\1`, 第二个分组就是`\2` ...)。 |
| `\k<name>` | 返回具体某个命名捕获分组的内容。                             |

例如：判断一个字符串数组所有元素是否完全相等

```javascript
let arr = ['abc', 'abc', 'abc'];
let str = arr.join(','); // abc,abc,abc
let reg = /(\w+),\1/; // 在这里，\1 就表示了第一个分组获取到的具体内容。

let match = reg.test(str);
console.log(match); // true
```



当然我们也可以使用命名捕获：

```javascript
let arr = ['item', 'item', 'item'];
let str = arr.join(',')+',';	// item,item,item,
let reg = /(?<element>\w+)(,)\k<element>\2/; // 等价于/(?<element>\w+)(,)\1\2/

let match = reg.test(str);
console.log(match); // true
```



##### 相关函数中的引用

在函数中有两种，一种是特殊字符指代，一种是函数参数。

例如在`string.prototype.replace`中，可以使用特殊字符 `$n` 来指代分组，同样的，数字 `n `代表分组索引。

```javascript
// 交换位置
let names = 'john,marry,jerry';
let new_names = names.replace(/(\w+),(\w+),(\w+)/, '$2,$3,$1');
console.log(new_names); 

// marry,jerry,john
```



也可以在函数参数中使用：

```javascript
// 交换位置
let names = 'john,marry,jerry';
let new_names = names.replace(/(\w+),(\w+),(\w+)/, function(content, n1, n2, n3){
    return `${n3} ${n2} ${n1}`;
});
console.log(new_names); 
// jerry marry john
```



具体的 `replace` 方法介绍在下面。



## 分支类型

当我们在匹配一个字符串时，如果存在其中某部分子串，既可以是A，又可以是B，就可以使用分支元字符。

| 元字符 | 定义 |
| ------ | ---- |
| `|`    | 或者 |



```javascript
let strArr = ['year','mouth','day'];
let reg = /mouth|day/;	//  匹配mouth或者day

strArr.forEach(str => {
     console.log(str, ": ", reg.test(str));
})


// year :  false
// mouth :  true
// day :  true
```





如果需要匹配这样一个字符串，既可以是A，又可以是B，同时又要满足C的时候，还可以结合分组来使用

例如匹配 `"window98 / window2000 / window10"` 这样一个字符串：

```javascript
let wins = ['window98','window2000', 'window10', 'window99'];
let reg = /window(98|2000|10)/;

wins.forEach(str => {
    console.log(str, ": ", reg.test(str));
})

// window98 :  true
// window2000 :  true
// window10 :  true
// window99 :  false
```



## JS中的正则

JS中与正则相关的主要有两种对象，`String` 对象和 `RegExp` 对象。

### RegExp对象


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/5/1731e6dd6df72f7b~tplv-t2oaga2asx-image.image)

#### 创建

##### 字面量

```javascript
let reg = /\d/
```



##### 构造函数

```javascript
let reg = new RegExp('ab+c', 'i'); // 使用字符串创建
let reg1 =new RegExp(/ab+c/, 'i'); // 使用字面量创建
```



##### 工厂函数

```javascript
let reg = RegExp('ab+c', 'i');	// 使用字符串创建
let reg1 =RegExp(/ab+c/, 'i');	// 使用字面量创建
```



注意：在使用RegExp方法创建正则表达式时，如果想要 `string` 类型的参数中使用元字符时，需要转义，例如：

```javascript
// 想要构建出 /\d+/ 这样的表达式
let reg = RegExp('\d+', 'i');	
// 此时创建出来的结果是 /d+/ 而不是目标表达式

// 正确的写法应该是：
// RegExp('\\d+', 'i');	
```


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/5/1731e6e2936db248~tplv-t2oaga2asx-image.image)

#### lastIndex

保存了当前正则实例上次匹配的位置。表示下次匹配从哪里开始。这个属性只有正则表达式使用了表示全局检索的 "`g`" 标志时，该属性才会起作用。所以当我们在使用全局匹配时，需要注意当前这个值是否被修改。**MDN**中是这样定义的：

> - 如果 `lastIndex` 大于字符串的长度，则 `regexp.test` 和 `regexp.exec` 将会匹配失败，然后 `lastIndex` 被设置为 0。
> - 如果 `lastIndex` 等于字符串的长度，且该正则表达式匹配空字符串，则该正则表达式匹配从 `lastIndex` 开始的字符串。（then the regular expression matches input starting at `lastIndex`.）
> - 如果 `lastIndex` 等于字符串的长度，且该正则表达式不匹配空字符串 ，则该正则表达式不匹配字符串，`lastIndex` 被设置为 0.。
> - 否则，`lastIndex` 被设置为紧随最近一次成功匹配的下一个位置。

例如

```javascript
let reg = /\d+/g;

function matchNumber(str){
    return reg.test(str);
}

console.log(matchNumber('123')); // true
console.log(matchNumber('456')); // false
```

上面的方法，我们期望返回两个`true`，但实际第一个确实返回`true`，第二个却返回了 `false`，这是因为我们的正则是一个全局变量，所以每次调用之后更改了`lastIndex`， 而每次的匹配都从`lastIndex`开始，所以第二次匹配，传入`456`实际会从第四个字符开始匹配。此时第四个字符为空，所以返回 `false`

```javascript
let reg = /\d+/g;

function matchNumber(str){
    console.log("reg.lastIndex is: ", reg.lastIndex);
    return reg.test(str);
}

console.log(matchNumber('123')); // true
console.log(matchNumber('456')); // false
console.log(matchNumber('4'));
/**
reg.lastIndex is:  0
true

reg.lastIndex is:  3
false

reg.lastIndex is:  0	
true
*/
```



#### 方法

##### test

测试当前字符串是否满足某个模式

参数：`str` 要匹配正则表达式的字符串。

返回：`boolean`值，表示是否匹配成功

例如判断当前字符串是否由11位数字组成：

```javascript
let reg = /^\d{11}$/;
let phone_number = '13577898320';

reg.test(phone_number); // true
```



##### exec

方法在一个指定字符串中执行一个搜索匹配。返回一个结果数组或 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)。

参数：`str` 要匹配正则表达式的字符串。

返回：一个数组：

| 对象             | 描述                               |
| ---------------- | ---------------------------------- |
| `[0]`            | 匹配的全部字符串                   |
| `[1], ...[*n* ]` | 括号中的分组捕获                   |
| `index`          | 匹配到的字符位于原始字符串的索引值 |
| `input`          | 原始字符串                         |
| `groups`         | 命名捕获对象                       |

⚠️：在js与正则相关的函数中，调用这个函数，会涉及到正则对象的状态。MDN中是这样解释的：

> 在设置了 [`global`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/global) 或 [`sticky`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky) 标志位的情况下（如 `/foo/g` or `/foo/y`），JavaScript [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 对象是**有状态**的。他们会将上次成功匹配后的位置记录在 [`lastIndex`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex) 属性中。使用此特性，`exec()` 可用来对单个字符串中的多次匹配结果进行逐条的遍历（包括捕获到的匹配），而相比之下， [`String.prototype.match()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match) 只会返回匹配到的结果。

提取出字符串中所有的数字

```javascript
let str = '各科成绩如下:数学: 121;语文: 120;英语: 138'
let reg = /\d+/g;
let result = null;
let i = 0;
while ((result = reg.exec(str)) !== null) {
	console.log(`-------------第${++i}次循环开始------------------`)
	console.log("result is: ", result); 
    console.log("reg.lastIndex is: ", reg.lastIndex); 
    console.log(`-------------第${++i}次循环结束------------------`)
}


/**
-------------第1次循环开始------------------
result is:  [ '121',
  index: 11,
  input: '各科成绩如下:数学: 121;语文: 120;英语: 138',
  groups: undefined ]
  
reg.lastIndex is:  14
-------------第1次循环结束------------------

-------------第2次循环开始------------------
result is:  [ '120',
  index: 19,
  input: '各科成绩如下:数学: 121;语文: 120;英语: 138',
  groups: undefined ]
reg.lastIndex is:  22
-------------第2次循环结束------------------

-------------第3次循环开始------------------
result is:  [ '138',
  index: 27,
  input: '各科成绩如下:数学: 121;语文: 120;英语: 138',
  groups: undefined ]
  
reg.lastIndex is:  30
-------------第3次循环结束------------------
*/

```



### String对象

String对象中涉及到正则的主要有4个方法：

- `search`
- `match`
- `replace`
- `split`

而其中，`match`方法中，正则表达式的 `global` 的不同值会影响返回结果。



#### search 

`search`方法很简单，主要用来查找子串的位置。

- 参数：一个正则表达式，必填
- 返回：找到的第一个子串在字符串中的索引值，如果没有找到就返回 `-1` 

```javascript
let str = 'find subString index: subString';
let reg1 = /subString/;
let reg2 = /substring/;
let reg3 = /subString/g;

str.search(reg1);		// 5
str.search(reg1);		// -1
str.search(reg1);		// 5 此时有无global都不影响结果
```



#### match

`match `方法中，主要用来提取字符串中满足条件的子串。

- 参数：正则表达式，，必填。这个正则表达式的 `global` 标志的值会影响返回结果：
  - `global == true` ：返回一个数组，包含了整个字符串中所有满足条件的子串。 
  - `global == false`： 返回
    - 数组`arr` ：
      - `arr[0]` 是当前正则匹配到的第一个子串的值
      - `arr[1] - arr[n]` ：分别是当前分组中捕获到的内容，有多少个分组就有多少项。且按照分组的顺序存储
    - `index` ：当前匹配到的子串在整个字符串中的索引值
    - `input` ：源字符串
    - `groups`：对象，包含了所有命名捕获组匹配到的内容 ，如果没有定义命名捕获组则为`undefined`

```javascript
let str = 'find subString index: subString';
let reg1 = /subString/;
let reg2 = /substring/;
let reg3 = /subString/g;

str.match(reg1);
/**
[
  'subString',
  index: 5,
  input: 'find subString index: subString',
  groups: undefined
]
**/
str.match(reg2);	// null
str.match(reg3);	// [ 'subString', 'subString' ]
```



#### replace

`replace`方法，就是用指定的值来替换源字符串中一些特定的子串。

- 参数：

  - `pattern`： 模式，指定被替换的子串类型。可以是一个 `String`，也可以是一个 `RegExp` 

    -  `String` 
    -  `RegExp`：当为`RegExp`对象时，如果 `global`标志为`false`，则只会替换匹配到的第一个子串。

  - `replacement`： 替换值，用于替换指定的子串。可以是一个 `String`，也可以是一个`Function`

    - `String`：如果pattern是一个`RegExp`，还可以使用一些特殊字符来插入命令：

      - | **`?`** | **插入一个 "$"**                 |
        | -------- | -------------------------------- |
        | **`$&`** | **插入匹配的子串**               |
        | **$`**   | **插入当前匹配的子串左边的内容** |
        | **`$'`** | **插入当前匹配的子串右边的内容** |
        | **`$n`** | **插入指定的捕获组内容**         |

    - `Function`：替换函数，可以指定返回的新字符串。只有第一个参数为正则时才生效。

      - > 你可以指定一个函数作为第二个参数。在这种情况下，当匹配执行后，该函数就会执行。 函数的返回值作为替换字符串。 (注意：上面提到的特殊替换参数在这里不能被使用。) 另外要注意的是，如果第一个参数是正则表达式，**并且其为全局匹配模式**，那么这个方法将被多次调用，每次匹配都会被调用。（源自MDN）

      - 参数：

        - `match`：每次匹配到的子串。
        - `p1,p2, ...`：假如`replace` 的 `pattern` 参数是一个 `RegExp`，则这里就是分别指代了各个分组匹配到的内容。
        - `offset`：匹配到的子字符串在原字符串中的偏移量。
        - `string`：源字符串。
        - `NamedCaptureGroup`： 命名捕获组匹配的对象

      - 返回：替换后的新字符串。

- 返回：替换后的新字符串。



有四种组合方式：

##### 组合一：string + string

```javascript
let str = '把名字替换成新的: john';
let new_str = str.replace('john', 'jerry');
console.log("new_str is: ", new_str);
// new_str is: 把名字替换成新的：jerry
```



##### 组合二：regexp + string

```javascript
let str = '把名字替换成新的：john';
let new_str = str.replace(/john/, 'jerry');
console.log("new_str is: ", new_str);
// new_str is: 把名字替换成新的：jerry
```



##### 组合三：regexp + 特殊符号

```javascript
let str = '交换名字：john,jerry'
let new_str = str.replace(/(\w+),(\w+)/, '$2,$1');
console.log("new_str is: ", new_str);
// new_str is: 交换名字：jerry,john
```



##### 组合四：regexp + function

```javascript
let str = '交换名字：john,jerry'
let new_str = str.replace(/(?<person1>\w+),(?<person2>\w+)/, function(match, n1, n2, offset, input, groups){
    console.log("match:",match);
    console.log("n1:",n1);
    console.log("n2:",n2);
    console.log("offset:",offset);
    console.log("input:",input);
    console.log("groups:",groups);
    return `${n2} && ${n1}`;
});
console.log("new_str is: ", new_str);

/**
    match: john,jerry
    n1: john
    n2: jerry
    offset: 5
    input: 交换名字：john,jerry
    groups: { person1: 'john', person2: 'jerry' }
    new_str is:  交换名字：jerry && john
*/
```



#### split

`split`方法主要用于分割字符串。

- 参数：
  - `separator` 分隔符，必填。可以是字符串或者正则表达式
  - `limit` 限制，可选。限定返回的分割片段数量。
- 返回：数组`arr`，包含了被分割后的片段。

##### `limit` 限制

```javascript
let str = '123,44a,234a,1';
let all = str.split(',');
let part = str.split(',', 2);
console.log("all is: ", all);
console.log("part is: ", part);
// all is:  [ '123', '44a', '234a', '1' ]
// part is:  [ '123', '44a' ]
```



##### 使用正则分割

```javascript
let str = 'year4mouth12days';
let label = str.split(/\d+/);
console.log("label is: ", label);
// label is:  [ 'year', 'mouth', 'days' ] 
```




## 参考

[JS正则表达式完整教程（略长）](https://juejin.im/post/6844903487155732494)

[Python 正则表达式操作指南](https://wizardforcel.gitbooks.io/py-re-guide/content/)

[正则表达式30分钟入门教程（正则表达式入门教程）](https://www.bookstack.cn/read/deerchao-regex/README.md)

[正则表达式零宽断言详解](https://www.cnblogs.com/onepixel/articles/7717789.html)

[MDN 正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

[MDN RegExp](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

[MDN String](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)