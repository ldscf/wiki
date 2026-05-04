---
source_title: Python 编程例子
categories:
- Develop
- Python
last_modified: '2025-08-05T03:14:00Z'
---
### 编写 斐波那契数列 的初始子序列
```
 a, b = 0,1
 while a < 10:
     print(a)
     a, b= b, a+b
```

或者
```
 def fab(max): 
     n, a, b = 0, 0, 1 
     while n < max: 
         print b 
         a, b = b, a + b 
         n = n + 1
 fab(5)
```

### 今天吃什么
```
 # 今天吃什么.py
 import fileinput,random
 lunch = list(fileinput.input(openhook=fileinput.hook_encoded('utf-8','')))
 print(random.choice(lunch))
```

菜单.txt 
```
 汉堡王
 庆丰包子
 魏家凉皮
 霸蛮米粉
 你的心跳酸菜鱼
 食堂
 山西刀削面
 KFC
 吉野家
```

调用： 
```
 python 今天吃什么.py 菜单.txt
```

### 今天吃什么水果-简版
```
 from random import *
 lis = ['苹果','梨','樱桃','车厘子','菠萝','波罗蜜','榴莲','橘子','橙子','柚子']
 choice(lis)
```

### 发扑克牌
```
 values = list(range(1,11)) + 'Jack Queen King'.split()     # 列表,list(range...
 suits = 'diamond clubs hearts spades'.split()              # [diamond,clubs,hearts,spades]
 deck = ['%s of %s'%(v, s) for v in values for s in suits]  # 两个for，最终是个列表
```
 
```
 from pprint import pprint  #自动换行
 pprint(deck[:12])          #只选12张（按顺序）显示如下 
```
```
 ['1 of diamond',
  '1 of clubs',
  '1 of hearts',
  '1 of spades',
  '2 of diamond',
  '2 of clubs',
  '2 of hearts',
  '2 of spades',
  '3 of diamond',
  '3 of clubs',
  '3 of hearts',
  '3 of spades']
```
```
 from random import shuffle #
 shuffle(deck)              #序列元素随机排序
 pprint(deck[:12])          #选12张（最终随机）显示如下  
```
```
 ['5 of hearts',
  '1 of hearts',
  '1 of diamond',
  '6 of clubs',
  'Jack of spades',
  '2 of diamond',
  'King of spades',
  '8 of diamond',
  'Queen of clubs',
  '6 of diamond',
  '9 of clubs',
  '9 of spades']
```

### 计算圆周率 π
```
 n = int(input("请输入想要计算到小数点后的位数:")) #输入字符转换为整数
 t = n+10                                     #多计算10位，防止尾数取舍的影响
 b = 10**t                                    #为算到小数点后t位，两边乘以10^t
 x1 = b*4//5                                  #取整求含4/5的首项
 x2 = b // -239                               #取整求含1/239的首项
 s = x1+x2                                    #求第一大项
 n *= 2                                       #设置下面循环的终点，即共计算n项
 for i in range(3, n, 2):                     #循环初值=3，末值n,步长=2
     x1 //= -25                               #取整求每个含1/5的项及符号
     x2 //= -57121                            #取整求每个含1/239的项及符号
     x = (x1+x2) // i                         #求两项之和，除以对应因子，取整
     s += x                                   #求总和
 pai = s*4                                    #求出π
 pai //= 10**10                               #舍掉后十位
 print(pai)                                   #输出圆周率π的值，不带小数点
```
