# 学习使用Lua来做Unity的热更新
## Lua的基础
参考[Lua菜鸟教程](http://www.runoob.com/lua/lua-basic-syntax.html)
### 写在前面
作为解释性语言，Lua和C#有着许多的不同。不过如果是有编程基础的同学，Lua学习并不是一件难事。

当前页面讲解的是基础的语法和使用逻辑。
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
- ..（字符串相加，C#的+）
- tostring(),tonumber()（数字与字符串的转换）
- string.upper,string.lower（大写化，小写化）

### Table表
在lua内table就像是C#中的list和dictionary的组合。当没有设置索引关键字时，table表则是默认**从1开始**填充（在lua里所有索引都是从1开始而非从0开始）。
```
mytable = {10,20,30,40}
```

在上述情况下mytable[1]的值为10。

我们同样可以通过设置索引关键字来填充table表。索引关键字分为number类型和string类型。number类只能通过table[number]的方式来索引，而strin类型则可以使用table[string]和table.string两种方式来索引。
```
mytable = {str = 10}
print(mytabel[str],mytable.str)
```

#### Table表的相关函数
table表同样提供多种函数，方便我们操作表内的所有存储的值。
- table.concat (table [sep[start[end]]]): 该方法将数组从start位置到end位置的所有元素, 元素间以指定的分隔符(sep)隔开的方式连接成一个字符串。但是该方法连接的字符只能是以number方式（这里必须用整型的）定义。
- table.insert (table, [pos], value) 在table的数组部分指定位置(pos)插入值为value的一个元素. pos参数可选, 默认则为数组部分末尾.
- table.remove (table [pos])返回table数组部分位于pos位置的元素. 其后的元素会被前移. pos参数可选, 默认为table长度, 即从最后一个元素删起。
- table.sort (table [comp])对给定的table进行升序排序。

## xLua
xLua是一种C#与Lua之间转换的插件，因为C#是编译型语言，它在对应平台上运行时已经编译完成，后续的修改将无法正常运行。所以我们采用lua这种解释性语言来完成代码逻辑编写。从而达到逻辑的热更新。

### Lua文件加载
#### 一、执行字符串
最基本是直接用LuaEnv.DoString执行一个字符串，当然，字符串得符合Lua语法
```
LuaEnv luaenv = new LuaEnv();
luaenv.DoString("print('hello world')");
```

但这种方式并不建议，更建议下面介绍这种方法。
#### 二、加载Lua文件
用lua的require函数即可
```
LuaEnv luaenv = new LuaEnv();
luaenv.DoString("require 'byfile'");
```

require实际上是调一个个的loader去加载，有一个成功就不再往下尝试，全失败则报文件找不到。
目前xLua除了原生的loader外，还添加了从Resource加载的loader，需要注意的是因为Resource只支持有限的后缀，放Resources下的lua文件得加上txt后缀.u也即如果你不使用自定义的Loader，直接使用require的方式来寻找byfile，那么byfile文件必须放置在Resources文件夹内，且后缀为.lua.txt。

建议的加载Lua脚本方式是：整个程序就一个DoString("require 'main'")，然后在main.lua加载其它脚本（类似lua脚本的命令行执行：lua main.lua）。
有童鞋会问：要是我的Lua文件是下载回来的，或者某个自定义的文件格式里头解压出来，或者需要解密等等，怎么办？问得好，xLua的自定义Loader可以满足这些需求。
#### 三、自定义Loader
在xLua加自定义loader是很简单的，只涉及到一个接口：

public delegate byte[] CustomLoader(ref string filepath);

public void LuaEnv.AddLoader(CustomLoader loader)

通过AddLoader可以注册个回调，该回调参数是字符串，lua代码里头调用require时，参数将会透传给回调，回调中就可以根据这个参数去加载指定文件，如果需要支持调试，需要把filepath修改为真实路径传出。该回调返回值是一个byte数组，如果为空表示该loader找不到，否则则为lua文件的内容。

我们首先需要通过AddLoader来添加自定义的Loader
```
LuaEnv luaEnv = new LuaEnv();
luaEnv.AddLoader(MyLoader);
luaEnv.DoString("require 'test'");
```

然后再在自定义的Loader中设置新的读取方式，下例为读取StreamingAssets中的lua脚本。
```
byte[] MyLoader(ref string Path)
{
	string LoadPath = Application.streamingAssetsPath + "/" + Path + ".lua.txt";
	return System.Text.Encoding.UTF8.GetBytes(File.ReadAllText(LoadPath));
}
```

有了这个就简单了，用IIPS的IFS？没问题。写个loader调用IIPS的接口读文件内容即可。文件已经加密？没问题，自己写loader读取文件解密后返回即可。。。

### C#访问Lua
我们可以通过C#脚本来访问lua的数据结构。
#### 一、获取一个全局基本数据类型
访问LuaEnv.Global就可以了，上面有个模版Get方法，可指定返回的类型。
```
int luaA = luaenv.Global.Get<int>("a");
string luaB = luaenv.Global.Get<string>("b");
bool luaC = luaenv.Global.Get<bool>("c");
```
#### 二、访问一个全局的table
lua内的table和普通的数据类型不一样，C#中没有table这个数据类型，所以我们需要指定映射到新的结构上来。

1、映射到普通class或struct

定义一个class，有对应于table的字段的public属性，而且有无参数构造函数即可，比如对于{f1 = "Name", f2 = 100}可以定义一个包含public string f1;public int f2;的class。

这种方式下xLua会帮你new一个实例，并把对应的字段赋值过去。table的属性可以多于或者少于class的属性。可以嵌套其它复杂类型。
```
person = {name = "TD", age = 24}
```

```
class Person
{
	public string name;
	public int age;
}

Person person = luaEnv.Global.Get<Person>("person");
```

当创建class时，需要获取的参数名称必须匹配，如果定义的不是name和age，而是其他名称，则无法获取到对应的值。

另外要注意的是，这个过程是值拷贝，如果class比较复杂，整体代价会比较大。而且修改class的字段值不会同步到table，反过来也不会。

2、映射到一个interface
这种方式依赖于生成代码（如果没生成代码会抛InvalidCastException异常），代码生成器会生成这个interface的实例，如果get一个属性，生成代码会get对应的table字段，如果set属性也会设置对应的字段。甚至可以通过interface的方法访问lua的函数。
```
[CSharpCallLua]
interface IPerson
{
	string name { get; set; }
	int age { get; set; }
}

IPerson person = luaEnv.Global.Get<IPerson>("person");
```

使用接口的方式和定义类很接近，都是定义需要获取的数据类型，然后通过Global.Get来获取。注意接口需要定义为**CSharpCallLua**。

3、更轻量级的by value方式：映射到Dictionary<>，List<>

当我们使用Dictionary时，可以映射到table中所有具有key值的参数，使用List时，可以映射到table中所有不具有key值的参数
```
person = {name = "TD", age = 24, 1, 2, 3, 1.1, "Me"}
```

我们在lua中定义了一个person的table。然后分别用Dictionary<> 和 List<>来获取。
```
List<object> personList = luaEnv.Global.Get<List<object>>("person");
Dictionary<string,object> personDic = luaEnv.Global.Get<Dictionary<string,object>>("person");
foreach (var item in personList)
{
    Debug.Log(item);
}

foreach (var item in personDic.Keys)
{
    Debug.Log(personDic[item]);
}
```

最后我们发现List部分能获取到1, 2, 3, 1.1, Me。而Dictionary能获取到TD，24。

4、另外一种by ref方式：映射到LuaTable类

xLua中设置了LuaTable类，可以全部获取到Table中所有的参数，这种方式好处是不需要生成代码，但也有一些问题，比如慢，比方式2要慢一个数量级，比如没有类型检查。

总的来说推荐使用接口，也就是第二种方法来获取Table数据。

#### 三、访问一个全局的function
仍然是用Get方法，不同的是类型映射。

1、映射到delegate（委托）

这种是建议的方式，性能好很多，而且类型安全。缺点是要生成代码（如果没生成代码会抛InvalidCastException异常）。那delegate要怎样声明呢？对于function的每个参数就声明一个输入类型的参数。
```
function add()
	print("add")
end
```

在lua中我们声明一个没有返回值的函数add，在C#中我们通过定义delegate来获取这个function。如果函数有返回值则定义时给出返回值类型替换void即可。
```
delegate void Add();
...
Add add = luaEnv.Global.Get<Add>("add");
```

多返回值要怎么处理？从左往右映射到c#的输出参数，输出参数包括返回值，out参数，ref参数。参数、返回值类型支持哪些呢？都支持，各种复杂类型，out，ref修饰的，甚至可以返回另外一个delegate。delegate的使用就更简单了，直接像个函数那样用就可以了。



2、映射到LuaFunction

这种方式的优缺点刚好和第一种相反。

使用也简单，LuaFunction上有个变参的Call函数，可以传任意类型，任意个数的参数，返回值是object的数组，对应于lua的多返回值。

#### 四、使用建议
1、访问lua全局数据，特别是table以及function，代价比较大，建议尽量少做，比如在初始化时把要调用的lua function获取一次（映射到delegate）后，保存下来，后续直接调用该delegate即可。table也类似。

2、如果lua测的实现的部分都以delegate和interface的方式提供，使用方可以完全和xLua解耦：由一个专门的模块负责xlua的初始化以及delegate、interface的映射，然后把这些delegate和interface设置到要用到它们的地方。

### Lua调用C#
#### new C#对象

你在C#这样new一个对象： 
```
var newGameObj = new UnityEngine.GameObject();
```

对应到Lua是这样： 
```
local newGameObj = CS.UnityEngine.GameObject()
```

二者定义方式基本类似，除了：
1、lua里头没有new关键字；

2、所有C#相关的都放到CS下，包括构造函数，静态成员属性、方法；

如果有多个构造函数呢？放心，xlua支持重载，比如你要调用GameObject的带一个string参数的构造函数，这么写：
```
local newGameObj2 = CS.UnityEngine.GameObject('helloworld')
```

#### 访问C#静态属性，方法
读静态属性
```
local time = CS.UnityEngine.Time.deltaTime
```

写静态属性
```
CS.UnityEngine.Time.timeScale = 0.5
```

调用静态方法
```
local camera = CS.UnityEngine.GameObject.Find("Main Camera")
camera.name = "Lua Camera"
```

小技巧：如果需要经常访问的类，可以先用局部变量引用后访问，除了减少敲代码的时间，还能提高性能：
```
local GameObject = CS.UnityEngine.GameObject
GameObject.Find('helloworld')
```

#### 访问C#成员属性，方法
如果是正常读写某条成员属性，可以直接按照C#的方式来进行读写
```
testobj.DMF

testobj.DMF = 1024

```

如果是读取成员方法，则需要将操作的参数传入，或者用“：”来代替“.”
```
local camera = CS.UnityEngine.GameObject.Find("Main Camera")
local cameraCom = camera:GetComponent("Camera")
--local cameraCom = camera.GetComponent(camera,"Camera")
CS.UnityEngine.GameObject.Destroy(cameraCom)
```

#### 参数的输入输出属性（out，ref）
Lua调用测的参数处理规则：C#的普通参数算一个输入形参，ref修饰的算一个输入形参，out不算，然后从左往右对应lua 调用测的实参列表；

Lua调用测的返回值处理规则：C#函数的返回值（如果有的话）算一个返回值，out算一个返回值，ref算一个返回值，然后从左往右对应lua的多返回值。

我们以Lua，C#的对应函数为例

```
--Lua
function func(a,b,c)
	...
	return x,c,d
end
```

```
//C#
int Func(string a, bool b, ref float c, out int d)
{
	...
	return x;
}
```


