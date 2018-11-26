# 学习使用Lua来做Unity的热更新
## Lua的基础
参考[Lua菜鸟教程](http://www.runoob.com/lua/lua-basic-syntax.html)
### 写在前面
作为解释性语言，Lua和C#有着许多的不同。不过如果是有编程基础的同学，Lua学习并不是一件难事。
### 定义变量
Lua不像C#一样，需要通过int、float、string，bool这种方式来进行变量的定义。
```
num = 1
str = "hello"
flo = 1.21
bol = true
```

在lua里是不是很放飞自我，所有变量都是按照赋值来自动定义（在lua中没有整数，所有数字默认为小数形式）。而且还没有;来做语句的结尾。

在lua里一共有8种数据类型，分别是nil、number、string、boolean、function、userdata、thread、table。

### 输出
lua的输出相对来说中规中矩，依然使用的print进行输出，但是和定义变量一样也不需要告诉输出函数，当前输入值的类型。
```
print（num,str,flo）
```

输出结果就是1  hello  1.21.

### 判断
在lua里同样使用if else来进行条件判断。但是很不幸lua并不使用花括号{}，所以lua采用了then和end来代替括号（明显增加了操作复杂度）。
```
if num >= 2 then
  print(str)
end
```

我们自然可以在多条件判断中，为我们的语句插入else.
```
if num >= 2 then
  print(str)
else
  print(flo)
end
```

那更多的条件判断则使用elseif（elseif是一个单词，不要打空格了）,而且还需要写then。不用花括号的代价有点高啊=。=
```
if num >= 2 then
  print(str)
elseif num < 2 then
  print(flo)
else
  print(num)
end
```

在逻辑判断中，与或非三类则是我们最常用的逻辑运算符。在lua里面分别为and、or、not。对应C#中的&&，||，！
```
if num >= 2 and bol then
  print(str)
elseif num < 2 or not bol then
  print(flo)
else
  print(num)
end
```

这个操作虽然大幅提高代码的理解，也使得习惯了符号的程序员浑身难受。

### 循环
lua循环判断和C#很像，分别是while、repeat和for循环，对应C#中的while，do-while和for循环

```
index = 1
while index <= 100 do
	print(index)
	index = index +1 
end
```

while循环使用了do和end来代替花括号

```
index = 1
repeat 
	print(index)
	index = index + 1
until index > 100
```

repeat则是将do-while修改成了repeat-until。如果你问我为啥不用原来的do-while，我觉得是前面while循环把while和do都用了，这里再用很容易出现错误。

```
for index = 1,100 do
	print(index)
end
```

从上述例子中我们可以看到，for循环的结构是
```
for var=初始值,终止值,步长 do  
    <执行体>  
end  
```

同样也是使用do-end代替花括号，然后缩写参数。

### 函数
函数作为八大基本数据类型，定义方式和我们先前看过的那些简单的一样。因为不需要定义返回值，所以前缀均为function，然后括号接参数，通过end来判断函数是否结束。
```
function max(num1, num2)
   if (num1 > num2) then
      result = num1;
   else
      result = num2;
   end
   return result; 
end
```

### 标准库（标准函数）
Lua内置了一些常用的函数来协助开发。
#### 数学处理的math相关函数
这类函数都以math打头，主要用于数学处理。才疏学浅只列简单函数
- math.abs（绝对值）
- math.sin,math.cos,math.tan（三角函数）
- math.random（随机数）
- math.max,math.min,math.maxinteger（最大最小值）
- math.sqrt（平方根）
#### 字符串处理相关函数
同上，也是介绍一些简单的函数
- ..（字符串相加，还我C#的+）
- tostring(),tonumber()（数字与字符串的转换）
- string.upper,string.lower（大写化，小写化）


