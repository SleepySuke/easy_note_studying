# Python

## 数据类型

数字类型：

1.整型（int）

2.浮点型（float）

3.布尔类型（bool） True、False

4.复数类型3+4i

非数字类型：

1.字符型（str）

2.列表（list）：[`1,2,3,4`]

3.元组（tuple）：(`1,2,3,4`)

4.字典（dict）：{`'name'`:`'小明'`，`'age'`:`18`}  键值对的形式

![](assets/1765373231367.png)

![](assets/1765373276120.png)

type()函数：

可以获取到变量的数据类型

type(变量) print(type(变量))

## 输入

获取用户的输入

input()

变量 = input()

1.代码从上到下执行，遇到input之后，会暂停等待，等待输入，不输入不结束

2.遇到回车，结束

3.输入的类型一定是字符串类型

## 类型转换

变量 = 要转换为的类型（原数据）

数据类型转换不会改变原来的数据的类型，会生成一个新的数据类型

![](assets/1765372963191.png)

## 格式化输出

![](assets/1765373348176.png)

![](assets/1765373405495.png)

小数默认显示6位，两个%%为转义，%s可以写任意类型

![](assets/1765373760862.png)

**F-string(f字符转格式化)**

3.6开始支持此种格式化方法

![](assets/1765373842568.png)

![](assets/1765374419823.png)

```
#!/usr/bin/env python
# -*- coding: UTF-8 -*-
'''
@Author ：自然醒
@Version ：1.0
'''
#格式化输出
name = '苏可锦'
age = 18
height = 1.75
print('我叫%s,年龄%d,身高%.2f'%(name,age,height))
print(f'我的名字是{name},年龄是{age},身高是{height:.2f}')
```

## 运算符

![](assets/1765374101505.png)

![](assets/1765374302174.png)

![](assets/1765374488107.png)

## if、elif、else结构

![](assets/1765374677190.png)

![](assets/1765374870022.png)

![](assets/1765374936581.png)

```
#!/usr/bin/env python
# -*- coding: UTF-8 -*-
'''
@Author ：自然醒
@Version ：1.0
'''
age = (int)(input('请输入年龄：'))
if age >= 18:
    print("eligible")
elif age < 18:
    print("ineligible")
else:
    print("invalid age")
```

## 循环结构

![](assets/1765375407724.png)

![](assets/1765375483807.png)

![](assets/1765375828147.png)

![](assets/1765375919874.png)

![](assets/1765375968634.png)

![](assets/1765376060661.png)

## 容器

数据序列，python中的数据类型，容器中可以存放多个数据

![](assets/1765636784064.png)

### 字符串定义

![](assets/1765636863702.png)

字符串本身包含引号时需要注意转换即特殊符号处理

![](assets/1765636980107.png)

字符串前边可加r""

![](assets/1765637081878.png)

字符串下标获取

![](assets/1765637153462.png)

切片使用

![](assets/1765637364810.png)

如果是负数索引的话则是字符的最后一个字符开始，如下图

![](assets/1765637737394.png)

步长为负数则是反转字符（逆置）字符串

只存在于-1的场景

**查找方法find**

![](assets/1765638159195.png)

**替换方法replace**

![](assets/1765638317468.png)

**拆分split**

![](assets/1765638528758.png)

**字符串连接join**

![](assets/1765638800522.png)

### 列表

![](assets/1765804055080.png)

**列表定义：**

![](assets/1765804070820.png)

```
#!/usr/bin/env python
# -*- coding: UTF-8 -*-
'''
@Author ：自然醒
@Version ：1.0
'''
#类实例化的方式创建列表，但是不常用
list1 = list()
print(type(list1), list1)
list2 = list('hello')
print(type(list2), list2)
```

**列表支持下标和切片：**

![](assets/1765804329600.png)

![](assets/1765804547097.png)

![](assets/1765804721723.png)

![](assets/1765804981684.png)

append一个列表的话则是整体添加至目标列表中，extend的话则是将列表元素拆开去添加

![](assets/1765805273384.png)

![](assets/1765805467721.png)

remove如果存在多个一样的数值则只删除第一个值

![](assets/1765805622730.png)

![](assets/1765805765486.png)

![](assets/1765805969444.png)

![](assets/1765806285244.png)

![](assets/1765806293917.png)

列表的嵌套即是Java中的二维数组一样的操作即可

![](assets/1765807062689.png)

### 元组

![](assets/1765807475377.png)

![](assets/1765807508472.png)

![](assets/1765807556259.png)

定义元组只有一个数据时，需要在后面添加一个逗号否则定义的则是一个数据

![](assets/1765807652117.png)

### 字典

字典dict，使用键值对存储数据，如map集合一样

![](assets/1766063722317.png)

![](assets/1766063916723.png)

**增加和修改操作**

![](assets/1766064002654.png)

![](assets/1766064248243.png)

![](assets/1766064452484.png)

![](assets/1766064688791.png)

![](assets/1766064861214.png)

## 函数

![](assets/1766064909536.png)

![](assets/1766065017496.png)

![](assets/1766065044379.png)

![](assets/1766065425637.png)

## 引用

![](assets/1766066066634.png)

![](assets/1766066392257.png)

python中数据的传递都是传递的引用，与java不同，java的引用传递是对象的值副本进行传递，实际上还是值传递，但python中是引用传递，主要在于python的对象可行性

可变对象、不可变对象

![](assets/1766325433328.png)

集合也是可变类型

![](assets/1766325512489.png)

对于不可变对象，此时只能通过重新赋值即重新创建对象才能改变

对于可变对象，可以通过索引等操作对其进行改变

对于列表的+=操作等于extend操作

### 组包和拆包

![](assets/1766327017194.png)

![](assets/1766327079768.png)

![](assets/1766327127626.png)

先看等号右边再看左边

a,b = b,a操作为先将b,a进行组包，随后再将a,b拆包

## 局部变量和全局变量

### 局部变量

![](assets/1766327498917.png)

### 全局变量

![](assets/1766327562810.png)

global需要放在第一行否则会报错







