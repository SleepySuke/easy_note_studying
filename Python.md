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



