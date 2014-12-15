#XPath

[TOC]



XPath 是一门在 XML 文档中查找信息的语言。
XPath 是 XSLT 中的主要元素。
XQuery 和 XPointer 均构建于 XPath 表达式之上

XPath 使用**路径表达式**来选取 XML 文档中的节点或节点集。节点是通过沿着路径 (path) 或者步 (steps) 来选取的。

##XPath 路径表达式
XPath 使用路径表达式来选取 XML 文档中的节点或者节点集。这些路径表达式和我们在常规的电脑文件系统中看到的表达式非常相似。

##XPath 标准函数
XPath 含有超过 100 个内建的函数。这些函数用于字符串值、数值、日期和时间比较、节点和 QName 处理、序列处理、逻辑值等等。

##XPath 术语
###节点

在 XPath 中，有七种类型的节点：元素、属性、文本、命名空间、处理指令、注释以及文档（根）节点。XML 文档是被作为节点树来对待的。树的根被称为文档节点或者根节点。

请看下面这个 XML 文档：

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<bookstore>
  <book>
    <title lang="en">Harry Potter</title>
    <author>J K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
</bookstore>```
上面的XML文档中的节点例子：

```
<bookstore> (文档节点)

<author>J K. Rowling</author> (元素节点)

lang="en" (属性节点)```
###基本值（或称原子值，Atomic value）

基本值是无父或无子的节点。

基本值的例子：

```
J K. Rowling

"en"```
###项目（Item）

项目是基本值或者节点。

##节点关系
###父（Parent）

每个元素以及属性都有一个父。

在下面的例子中，book 元素是 title、author、year 以及 price 元素的父：
```
<book>
  <title>Harry Potter</title>
  <author>J K. Rowling</author>
  <year>2005</year>
  <price>29.99</price>
</book>```
###子（Children）

元素节点可有零个、一个或多个子。

在下面的例子中，title、author、year 以及 price 元素都是 book 元素的子：
```
<book>
  <title>Harry Potter</title>
  <author>J K. Rowling</author>
  <year>2005</year>
  <price>29.99</price>
</book>```
###同胞（Sibling）

拥有相同的父的节点

在下面的例子中，title、author、year 以及 price 元素都是同胞：
```
<book>
  <title>Harry Potter</title>
  <author>J K. Rowling</author>
  <year>2005</year>
  <price>29.99</price>
</book>```
###先辈（Ancestor）

某节点的父、父的父，等等。

在下面的例子中，title 元素的先辈是 book 元素和 bookstore 元素：
```
<bookstore>

<book>
  <title>Harry Potter</title>
  <author>J K. Rowling</author>
  <year>2005</year>
  <price>29.99</price>
</book>```

</bookstore>
###后代（Descendant）

某个节点的子，子的子，等等。

在下面的例子中，bookstore 的后代是 book、title、author、year 以及 price 元素：
```
<bookstore>
	<book>
	  <title>Harry Potter</title>
	  <author>J K. Rowling</author>
	  <year>2005</year>
	  <price>29.99</price>
	</book>
</bookstore>```


##XML语法
###XML实例

```
<?xml version="1.0" encoding="ISO-8859-1"?>

<bookstore>

<book>
  <title lang="eng">Harry Potter</title>
  <price>29.99</price>
</book>

<book>
  <title lang="eng">Learning XML</title>
  <price>39.95</price>
</book>

</bookstore>
```
###选取节点
XPath 使用路径表达式在 XML 文档中选取节点。节点是通过沿着路径或者 step 来选取的。 

下面列出了最有用的路径表达式：

| 表达式 | 描述 |
|--------|--------|
|nodename	|选取此节点的所有子节点。|
|/	|从根节点选取。|
|//	|从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。|
|.	|选取当前节点。|
|..	|选取当前节点的父节点。|
|@	|选取属性。|


在下面的表格中，我们已列出了一些路径表达式以及表达式的结果：

###谓语（Predicates）
谓语用来查找某个特定的节点或者包含某个指定的值的节点。

谓语被嵌在方括号中。

在下面的表格中，我们列出了带有谓语的一些路径表达式，以及表达式的结果：

###选取未知节点
XPath 通配符可用来选取未知的 XML 元素。

###选取若干路径
通过在路径表达式中使用"|"运算符，您可以选取若干个路径。

在下面的表格中，我们列出了一些路径表达式，以及这些表达式的结果：

##XPath 轴（Axes）
###XML 实例文档
我们将在下面的例子中使用此 XML 文档：
```
<?xml version="1.0" encoding="ISO-8859-1"?>

<bookstore>

<book>
  <title lang="eng">Harry Potter</title>
  <price>29.99</price>
</book>

<book>
  <title lang="eng">Learning XML</title>
  <price>39.95</price>
</book>

</bookstore>```
###XPath 轴（Axes）
轴可定义相对于当前节点的节点集。

##XPath 运算符
XPath 表达式可返回节点集、字符串、逻辑值以及数字。
###XPath 运算符
下面列出了可用在 XPath 表达式中的运算符：






