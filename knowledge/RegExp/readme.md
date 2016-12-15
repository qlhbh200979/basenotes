# RegExp

> 典型的搜索和替换操作要求您提供与预期的搜索结果匹配的确切文本。而正则表达式 有的优势：
> + 测试字符串内的模式(RegExp.prototype.test)
> + 替换文本(String.prototype.replace)
> + 基于模式匹配字符串从中提取子字符串(RegExp.prototype.exec、String.prototype.match)

## 字符簇

> 除了指定具体字符外，还存在一些字符选择范围

常见的字符簇如下表：

|字符         | 描述                      |
|-------------|---------------------------|
|\d           |数字                       |
|\D           |非数字                     |
|\w           |字母数字下划线             |
|\W           |非字母数字下划线           |
|\s           |空白符[\f\r\n\t\v]         |
|\S           |非空白符[^\f\r\n\t\v]      |
|.            |除\n意外任何字符           |

除了系统规定的字符簇外，允许自定义字符簇：
+ 使用[]号表示匹配字符可选范围
+ 可以使用^表示非
+ 可以使用-表示连续字符范围


```shell
# 表示为字母数字自定义字符簇
[a-zA-Z0-9]

# 表示非数字自定义字符簇
[^0-9]
```

**注意：不管是单个字符，还是字符簇都只是表示为***单字符***匹配**


## 限定符

> 限定符用来指定正则表达式的一个给定组件必须要出现多少次才能满足匹配。有*或+或?或{n}或{n,}或{n,m}共6种。

常见的限定符有：

|字符                | 描述                     |
|--------------------|--------------------------|
|?                   |表示匹配子表达式0或1次      |
|*                   |表示匹配子表达式0到N次      |
|+                   |表示匹配子表达式1到N次      |
|{n}                 |表示匹配子表达式n次         |
|{n,}                |表示至少匹配子表达式n次     |
|{n,m}               |表示匹配子表达式n到m次      |

#### 贪婪与非贪婪匹配

上文中的6中表达式均是贪婪的，因为它们会尽可能多的匹配文字。

```javascript 
// 输出结果：["<H1 title="aaaa">aaa<H1><H1 title="bbbb">bbbb<H1>"]
'<H1 title="aaaa">aaa<H1><H1 title="bbbb">bbbb<H1>'.match(/<.{0,}>/);
'<H1 title="aaaa">aaa<H1><H1 title="bbbb">bbbb<H1>'.match(/<.*>/);

```

如果在上述6中限定符后面加上?即为**最小匹配**或**非贪婪匹配**：
+ +?
+ *?
+ {n,}?

```javascript
// 输出结果：["<H1 title="aaaa">"]
'<H1 title="aaaa">aaa<H1><H1 title="bbbb">bbbb<H1>'.match(/<.{0,}?>/);
'<H1 title="aaaa">aaa<H1><H1 title="bbbb">bbbb<H1>'.match(/<.*?>/);
```

当然由于?、{n,m}、{n}最小匹配并没有意义，所以一般来说不会有用

## 定位符

> 定位符用来描述字符串或单词的边界,定位符使您能够将正则表达式固定到行首或行尾。

正则表达式的限定符有：

|字符            |描述                  |
|----------------|----------------------|
|^               |开头符                |
|$               |结束符                |
|\b              |边界符，包含开头、结尾以及字符直接空格 |
|\B              |非边界符                               |


```javascript
// 只匹配以mac开头的字符["mac"]
'mac123 mac456 456mac'.match(/^mac/g);

//匹配开头、结尾以及前面有空格字符["mac", "mac"]
'mac123 mac456 456mac'.match(/\bmac/g);

```

注意：^是一个比较特殊字符，放在表达式最前面表示***定位***，放到[]或其他地方表示***非当前字符或字符簇***

## 表达式选项

| 符号用于表达式可选项，区分前后表达式为***或***选项：

```javascript
//["a", "btt"]
'bacbtt'.match(/a|btt/g);

//["linqtest", "benytest"]
'linqtest benytest'.match(/(linq|beny)test/g);
```

选项时区分的不同的字符串或表达式，让模式多了一种选择。

## 分组

 基于上述所有匹配模式都基于单字符，只能对单个字符进行**限定**、**定位**、**字符簇**等，如果需要对多字符进行限定，那就需要用到分组{}

> 分组（{}）常用于限制匹配范围，但也用于创建子表达式,创建的子表达式会在非global或/g模式下，将匹配结果放到匹配后面

常见分组玩法：

|字符              |描述                          |
|------------------|------------------------------|
|(pattern)         |捕获匹配，匹配 pattern并获取这一匹配，子表达式匹配内容可以使用\n访问                  |
|(?:pattern)       |非捕获匹配，匹配 pattern 但不获取匹配结果          |
|(?=pattern)       |正向预查，在任何匹配 pattern 的字符串开始处匹配查找字符串。这是一个非获取匹配                       |
|(?!pattern)       |负向预查，在任何不匹配 pattern 的字符串开始处匹配查找字符串。这是一个非获取匹配                       |

捕获匹配与非捕获匹配：

```javascript
//匹配连续出现两次的字符["Is is", "of of", "up up"]
// \1表示捕获匹配中第一个分组匹配内容
// \b表示定位符
'Is is the cost of of gasoline going up up?'.match(/\b([a-z]+) \1\b/gi);

// 去除左右两边空格
'  test    '.replace(/(?:^\s*)|(?:\s*$)/g, "");
```

正向预查与反向预查

```javascript
//["Windows ", "Windows "]  能够很好地匹配到Windows 98与Windows NT
'Windows 98 Windows NTWindows 10'.match(/Windows (?=95|98|NT|2000)/g)

//["Windows "] 能够很好地匹配到非Windows 98与Windows NT的Windows 10
'Windows 98 Windows NTWindows 10'.match(/Windows (?!95|98|NT|2000)/g)

```

## 常用表达式

+ /^(https|http)+(:\/\/)+([\w-]+\.)?([\w-]+)\.([\w-]+)(:\d+)?(\/.\S*)?$/gi.test('http://www.baidu.com/')


