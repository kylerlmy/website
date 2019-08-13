---
title: Python 基础入门（一）
tags: Python basic
comments: true
categories: Python
date: 2019-01-28
---

### 数据类型

#### 字面常量
是一个常量，值不可以被改变。使用的即为该常量字面意义上的值或内容。
举例：5、“this is a string”、“这是个文本”等。

#### 数字
数字分为两种类型：整型（Intergers）与浮点数（Floats）。
*注意：没有单独的long或short型。int 类型可以指定任何大小的整数。*

#### 对象
Python是强（Strongly）面向对象的，因为所有的一切都是对象， 包括数字、字符串与函数。

#### 字符串
一个字符串是字符的序列（sequence）。
*注意：字符串是不可变的，一旦创造了一个字符串，就不可以再改变。    python 中没有单独的 char 类型。你在程序中使用的所有字符串都是str类下的对象，可以使用 help(str)查看详细信息。*

##### 单引号与双引号
可以使用单引号或双引号指定字符串。被双引号包括的字符串和被单引号包括的字符串的工作机制完全相同。
*注意：所有引号内的空间，比如，空格和制表符，都将按照原样保留。*

##### 三引号
可以是能够用三个引号—— ''' 或 """ 来指定多行字符串。可以在三个引号直接使用单引号与双引号。
例如：
`'''这是一段多行字符串。这是它的第一行。`
`This is the second line.`
`"What's your name?," I asked.`
`He said "Bond, James Bond."`
`'''`

##### 字符串格式化
一个字符串可以使用某些特定的格式（Specification），随后， format 方法将被调用，该方法中将与之相应的参数替换这些格式。

应用举例：
```python

# 对于浮点数 '0.333' 保留小数点(.)后三位
print('{0:.3f}'.format(1.0/3))

# 使用下划线填充文本，并保持文字处于中间位置
# 使用 (^) 定义 '___hello___'字符串长度为 11
print('{0:_^11}'.format('hello'))

# 基于关键词输出 'Swaroop wrote A Byte of Python'
print('{name} wrote {book}'.format(name='Swaroop', book='A Byte of Python'))

#重复字符串,输出'lalala'
print("{0}".format('la'*3))
```
*注意：print 方法总是会以一个不可见的 "换行"字符 （\n） 结尾，可以使用 end 指定以空白结尾 *

##### 转义
使用 “反斜杠”指定转义字符。
例如：在一个字符串中输入一个引号 .
'what's your name?'  在python中的正确表达是：'what\\'s your name?'只有这样，当遇到第一个引号时，才不至于当成是一个字符串的结束符号。

其他常用转义符号：
放斜杠 "\\\\"
制表符 “\\\t”
*在一个字符串中，一个放在一行末尾的放斜杠表示，字符串将在下一行继续，当不会添加新行。*

##### 原始字符串
如果需要指定一些为经过处理的字符串，比如转义序列，可以在字符串前添加 r 或者 R 来指定一个原始字符串。
例如：
`print(r"hello world \n")`

### 运算符与表达式


#### 运算符
基本运算符
*  \+  （加）
* \-    （减）
* \*    （乘）
* \**   （乘方）
* \/    （除）
* \//   （整除）
* %    （取模）

位运算符
* << （左移）
* \>> （右移）
*  & （按位与）
* | （按位或）
* ^ （按位异或）
* ~ （按位取反）

比较运算符
* < （小于）
* \> （大于）
* \<= （小于等于）
* \>= （大于等于）
* == （等于）
* != （不等于）

布尔运算符
* not （布尔“非”）
* and （布尔“与”）
* or （布尔“或”）

#### 求值顺序
{% asset_img priority.png 运算优先级 %}

上图中为从最低优先级到最高优先级的优先级表。
#### 结合性
运算符通常由左至右结合。这意味着具有相同优先级的运算符将从左至右的方式依次进行求值。

### 控制流

#### if 语句
if 语句在结尾处包含一个冒号——用于向 Python 指定接下来会有一块语句在后头。
elif 和 else 同样都必须有一个冒号在其逻辑行的末尾，后面跟着与它们相应的语句块（别忘了恰当的缩进）。

示例：
```python
number = 23
guess = int(input('Enter an integer : '))
if guess == number:
    # 新块从这里开始
    print('Congratulations, you guessed it.')
    print('(but you do not win any prizes!)')
    # 新块在这里结束
elif guess < number:
    # 另一代码块
    print('No, it is a little higher than that')
    # 你可以在此做任何你希望在该代码块内进行的事情
else:
    print('No, it is a little lower than that')
    # 你必须通过猜测一个大于（>）设置数的数字来到达这里。
print('Done'
# 这最后一句语句将在
# if 语句执行完毕后执行。
```
*注意判断条件中没有小括号*

#### while 语句
while 语句是 循环语句的一种。 while 语句同样可以拥有 else 子句作为可选选项。else 代码块在 while 循环的条件变为 False 时开始执行。

#### for 语句
for...in 语句是另一种循环语句，其特点是会在一系列对象上进行迭代（Iterates），即它会遍历序列中的每一个项目。类型与C#中的foreach。
与while相同， for循环同样可以拥有 else 子句作为可选选项。当循环中包含else子句时，它总会在 for 循环结束后开始执行，除非程序遇到了 break 语句。

### 函数
函数（Functions）是指可重复使用的程序片段。它们允许你为某个代码块赋予名字，允许你通过这一特殊的名字在你的程序任何地方来运行代码块，并可重复任何次数。这就是所谓的调（Calling）函数可以通过关键字 def 来定义。这一关键字后跟一个函数的标识符名称，再跟一对圆括号，其中可以包括一些变量的名称，再以冒号结尾，结束这一行。随后而来的语句块是函数的一部分。

#### 函数参数
在定义函数时给定的名称称作“形参”（Parameters）。
在调用函数时你所提供给函数的值称作“实参”（Arguments)。
函数中的参数通过将其放置在用以定义函数的一对圆括号中指定，并通过逗号予以分隔。当我们调用函数时，我们以同样的形式提供需要的值。

示例：
```python
def print_max(a, b):
    if a > b:
        print(a, 'is maximum')
    elif a == b:
        print(a, 'is equal to', b)
    else:
        print(b, 'is maximum')
# 直接传递字面值
print_max(3, 4)
x = 5
y = 7
# 以参数的形式传递
print_max(x, y)
```
#### 局部变量
当你在一个函数的定义中声明变量时，它们不会以任何方式与身处函数之外但具有相同名称的变量产生关系，也就是说，这些变量名只存在于函数这一局部（Local）。这被称为变量的作用域（Scope）。

#### global 语句
如果你想给一个在程序顶层的变量赋值（也就是说它不存在于任何作用域中，无论是函数还是类），那么你必须告诉 Python 这一变量并非局部的，而是全局（Global）的。我们需要通global 语句来完成这件事。

示例：
```python
def func():
    global x
    print('x is', x)
    x = 2
    print('Changed global x to', x)
    
func()
print('Value of x is', x)
```
输出：

x is 50
Changed global x to 2
Value of x is 2

global 语句用以声明 x 是一个全局变量——因此，当我们在函数中为 x 进行赋值时，这一改动将影响到我们在主代码块中使用的 x 的值。

#### 默认参数
你可能为希望使一些参数可选并使用默认的值，以避免用户不想为他们提供值的情况。默认参数值可以有效帮助解决这一情况。你可以通过在函数定义时附加一个赋值运算符（=）来为参数指定默认参数值。
示例:
```python
def say(message, times=1):
    print(message * times)
    
say('Hello')
say('World', 5)
```
*只有那些位于参数列表末尾的参数才能被赋予默认参数值，即在函数的参数列表中拥有默认参数值的参数不能位于没有默认参数值的参数之前。*

#### 关键字参数
如果你有一些具有许多参数的函数，而你又希望只对其中的一些进行指定，那么你可以通过命名它们来给这些参数赋值。这就是关键字参数。

关键字参数的优点：
* 不需要考虑参数的顺序，函数的使用将更加容易
* 可以只对那些希望赋值的参数赋值，只要其它的参数都具有默认参数值即可。

示例：
```python
def func(a, b=5, c=10):
    print('a is', a, 'and b is', b, 'and c is', c)
    
func(3, 7)
func(25, c=24)
func(c=50, a=100)
```

#### 可变参数
有时你可能想定义的函数里面能够有任意数量的变量，也就是参数数量是可变的，这可以通过使用星号来实现。
声明一个如  `*param` 的星号参数时，从此处开始直到结束的所有位置参数（Positional Arguments）都将被收集并汇集成一个称为“param”的元组（Tuple）。
声明一个如 `**param` 的双星号参数时，从此处开始直至结束的所有关键字参数都将被收集并汇集成一个名为 param 的字典（Dictionary）。

实例：
```python
def total(a=5, *numbers, **phonebook):
    print('a', a)
    #遍历元组中的所有项目
    for single_item in numbers:
        print('single_item', single_item)
        
    #遍历字典中的所有项目
    for first_part, second_part in phonebook.items():
        print(first_part,second_part)
        
print(total(10,1,2,3,Jack=1123,John=2231,Inge=1560))
```
### DocStrings
文档字符串所约定的是一串多行字符串，其中第一行以某一大写字母开始，以句号结束。第二行为空行，后跟的第三行开始是任何详细的解释说明。
同样适用于类和模块的描述。
可以通过使用函数的 ` \_\_doc__`（注意其中的双下划线）属性（属于函数的名称）来获取函数的文档字符串属性。
Python 的 help() 函数，它所做的便是获取函数的  `\_\_doc__` 属性并以一种整洁的方式将其呈现给你。
自动化工具可以以这种方式检索你的程序中的文档。

示例：
```python
def print_max(x, y):
    '''打印两个数值中的最大数。

    这两个数都应该是整数'''
    # 如果可能，将其转换至整数类型
    x = int(x)
    y = int(y)
    
    if x > y:
        print(x, 'is maximum')
    else:
        print(y, 'is maximum')
    
print_max(3, 5)
print(print_max.__doc__)
```
### 模块
可以使用模块来在你编写的别的程序中重用一些函数。
编写模块有很多种方法，其中最简单的一种是创建一个包含函数与变量，并且以 .py 为后缀的文件。
另一种方法是使用编写 Python 解释器本身的本地语言来编写模块。例 如，你可以使用 C语言来撰写 Python 模块，并且在编译后，你可以通过标准 Python 解释器在你的 Python 代码中使用它们。
一个模块可以被其它程序导入并运用其功能。

#### 按字节码编译的 .pyc文件
导入一个模块是一件代价高昂的事情，因此 Python 引入了一些技巧使其能够更快速的完成。其中一种方式便是创建按字节码编译的（Byte-Compiled）文件，这一文件以  `.pyc` 为其扩展名，是将 Python 转换成中间形式的文件。
*注意：这些 .pyc 文件通常会创建在对应的 .py 文件所处的目录中。如果 Python 没有相应的权限对这一目录进行写入文件的操作，那么 .pyc 文件将不会被创建。*

#### from .. import 语句
示例
`from math import sqrt`

一般来说，你应该尽量避免使用 `from...import` 语句，而去使用 import 语句。这是为了避免在你的程序中出现名称冲突，同时也为了使程序更加易读。

#### 模块的 \_\_name__
每个模块都有一个名称，而模块中的语句可以找到它们所处的模块的名称。这对于确定模块是独立运行的还是被导入进来运行的这一特定目的来说非常有用。
可以使用 \_\_name__ 属性来判断所引用的模块是，其自身的还是导入的。以下针对不同的模块以不同的方式运行。

示例:
```python
if __name__ == '__main__':
    print('This program is being run by itself')
else:
    print('I am being imported from another module')
```
>Python 的一大指导原则是“明了胜过晦涩” 。你可以通过在 Python 中运行 import this来了解更多内容。

#### dir 函数
dir() 函数能够返回由对象所定义的名称列表。 如果这一对象是一个模块，则该列表会包括函数内所定义的函数、类与变量。
该函数接受参数。 如果参数是模块名称，函数将返回这一指定模块的名称列表。 如果没有提供参数，函数将返回当前模块的名称列表。

#### 包
用以组织程序的层次结构。变量通常位于函数内部，函数与全局变量通常位于模块内部，模块通常位于包的内部。
包是指一个包含模块与一个特殊的 `\_\_init__.py` 文件的文件夹。
如同函数是程序中的可重用部分那样，模块是一种可重用的程序。包是用以组织模块的另一种层次结构。

### 数据结构
数据结构（Data Structures）它们只是一种结构，能够将一些数据聚合在一起。换句话说，它们是用来存储一系列相关数据的集合。
Python 中有四种内置的数据结构，列表（List）、元组（Tuple）、字典（Dictionary）和集合（Set）。

#### 列表
列表 是一种用于保存一系列有序项目的集合。列表也是一个序列。
列表应该用方括号括起来，列表中的各个子项以逗号分隔。一旦你创建
了一张列表，你可以添加、移除或搜索列表中的项。由于列表可以添加和删除，所以说，说列表是一种可变的（Mutable）数据类型。

例如：
`shoplist = ['apple', 'mango', 'carrot', 'banana']`

列表是使用对象与类的实例。
列表拥有很多实例成员，可以通过help(list)查看。

#### 元组
元组（Tuple）用于将多个对象保存到一起。你可以将它们近似地看作列表，但是元组不能提供列表类能够提供给你的广泛的功能。元组的一大特征类似于字符串，它们是不可变的，也就是说，你不能编辑或更改元组。
元组是通过特别指定项目来定义的，在指定项目时，你可以给它们加上括号，并在括号内部用逗号进行分隔。

例如:
`zoo = ('python', 'elephant', 'penguin')`

元组通常用于保证某一语句或某一用户定义的函数可以安全地采用一组数值，意即元组内的数值不会改变。
*注：包含 0 或 1 个项目的元组;一个空的元组由一对圆括号构成，就像 myempty = () 这样。然而，一个只拥有一个项目的元组并不像这样简单。你必须在第一个（也是唯一一个）项目的后面加上一个逗号来指定它,例如：*`singleton = (2, )` 


#### 字典
字典将键（Keys）与值（Values）联立到一起。在这里要注意到键值必须是唯一的。
另外要注意的是你只能使用不可变的对象（如字符串）作为字典的键值，但是你可以使用可变或不可变的对象作为字典中的值。也就是说你只能使用简单对象作为键值。
在字典中，你可以使用 `d = {key : value1 , key2 : value2} `这样的形式，来成对地指定键值与值。在这里要注意到成对的键值与值之间使用冒号分隔，而每一对键值与值则使用逗号进行区分，它们全都由一对花括号括起来。
字典中的成对的键值—值配对不会以任何方式进行排序。如果你希望为它们
安排一个特别的次序，只能在使用它们之前自行进行排序。

示例:
```python
# “ab”是地址（Address）簿（Book）的缩写

ab = {
'Swaroop': 'swaroop@swaroopch.com',
'Larry': 'larry@wall.org',
'Matsumoto': 'matz@ruby-lang.org',
'Spammer': 'spammer@hotmail.com'
}

print("Swaroop's address is", ab['Swaroop'])

# 删除一对键值—值配对
del ab['Spammer']

print('\nThere are {} contacts in the address-book\n'.format(len(ab)))

for name, address in ab.items():
    print('Contact {} at {}'.format(name, address))
    
# 添加一对键值—值配对
ab['Guido'] = 'guido@python.org'

if 'Guido' in ab:
    print("\nGuido's address is", ab['Guido'])
```

可以通过使用字典的 items 方法来访问字典中的每一对键值—值配对信息，这一操作将返回一份包含元组的列表，每一元组中则包含了每一对相应的信息
想增加一堆新的键值—值配对，我们可以简单地通过使用索引运算符访问一个键值并为其分配与之相应的值。
可以使用 in 运算符来检查某对键值是否存在。

#### 序列
列表、元组和字符串可以看作序列（Sequence）的某种表现形式。
序列的主要功能是判断某个项是否存在（也就是 in 与 not in 表达式）和索引操作（Indexing Operations），它们能够允许我们直接获取序列中的特定项目。
序列的三种形态——列表、元组与字符串，都拥有一种切片（Slicing）运算符，它能够允许我们访问序列中的某段切片——也就是序列之中的一部分。

示例：
```python
shoplist = ['apple', 'mango', 'carrot', 'banana']
name = 'swaroop'

# Indexing or 'Subscription' operation #
# 索引或“下标（Subscription）”操作符 #
print('Item 0 is', shoplist[0])
print('Item 1 is', shoplist[1])
print('Item 2 is', shoplist[2])
print('Item 3 is', shoplist[3])
print('Item -1 is', shoplist[-1])
print('Item -2 is', shoplist[-2])
print('Character 0 is', name[0])

# Slicing on a list #
print('Item 1 to 3 is', shoplist[1:3])
print('Item 2 to end is', shoplist[2:])
print('Item 1 to -1 is', shoplist[1:-1])
print('Item start to end is', shoplist[:])

# 从某一字符串中切片 #
print('characters 1 to 3 is', name[1:3])
print('characters 2 to end is', name[2:])
print('characters 1 to -1 is', name[1:-1])
print('characters start to end is', name[:])
```
索引操作也可以使用负数，在这种情况下，位置计数将从队列的末尾开始。
你需要通过指定序列名称来进行序列操作，在指定时序列名称后面可以跟一对数字——这是可选的操作，这一对数字使用方括号括起，并使用冒号分隔。需要注意这里的数字是可选的，冒号却不是。
在切片操作中，第一个数字（冒号前面的那位）指的是切片开始的位置，第二个数字（冒号后面的那位）指的是切片结束的位置。如果第一位数字没有指定，Python 将会从序列的起始处开始操作。如果第二个数字留空，Python 将会在序列的末尾结束操作。要注意的是切片操作会在开始处返回 start，并在 end 前面的位置结束工作。也就是说，序列切片将包括起始位置，但不包括结束位置。

#### 集合
集合（Set）是简单对象的无序集合（Collection）。当集合中的项是否存在比次序或其出现次数更加重要时，我们就会使用集合。
通过使用集合，你可以测试某些对象是否存在，检查它们是否是其它集合的子集，找到两个集合的交集，等等。

#### 引用
当你创建了一个对象并将其分配给某个变量时，变量只会查阅（Refer）某个对象，并且它也不会代表对象本身。也就是说，变量名只是指向你计算机内存中存储了相应对象的那一部分。这叫作将名称绑定（Binding）给那一个对象。