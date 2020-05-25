## python-面向对象-19-object根类/weakref弱引用

### 1.object根类
`object`类是所有类的父类，因此所有的类都有object类的属性和方法。我们显然有必要深入研究一下object类的结构。对于我们深入学习Python很有好处。   
其实我们在前面第17讲介绍继承的时候，其实就已经介绍`object`根类的一部分，包括使用`mro()`函数查看类的继承关系，这次我们从另外角度来说明`object`根类。

还是通过一个例子开始：

```python
class Person:
    count = 0
    
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def say_age(self):
        print("{0}的年龄是:{1}".format(self.name, self.age))

obj = object()
print(dir(obj))

s = Person("聂发俊", 100)
print(dir(s))
```
运行结果：
```
['__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'age', 'count', 'name', 'say_age']
```

程序说明：  
这段程序已经很熟悉了，运行原理就不过多赘述了。我们对比一下对象`obj`和对象`s`所返回的属性列表，发现相比增加了一下几个属性：`__dir__`, `__module__`,`__weakref__`,`age`,`count`,`name`,`say_age`。首先说简单的：
1. 实例属性: `name`,`age`
2. 类属性：`count`
3. 实例方法：`say_age`

### 2. `__dir__`属性列表
只有存在类方法(静态方法)、类属性、实例属性、实例方法中至少一项，才会有`__dict__`属性。如上面例子中的对象`obj`，就没有`__dict__`属性。


### 3.`__module__`模块属性
代表当前对象运行的模块
```python
class Person:
    pass

p = Person()
print(p.__module__)
```
运行结果：
```
__main__
```
程序说明：默认运行在`__main__`模块下。

### 4.`__weakref__`弱引用属性   
说起弱引用，就不得不说前面提到的Python垃圾回收机制，垃圾回收机制最核心的机制就是引用计数。`weakref`的弱引用是相对于引用计数而言的，引用计数的方式也可以叫做常规引用或者强引用(后面个人定义，如果不对，请多多指教。)   
相同点： **无论是常规引用还是弱引用，都能够通过引用的方式获取到被引用对象的地址，换句话说，就是可以具有被引用对象的想用的操作**
区别：**常规引用会增加引用计数，但是弱引用不会增加引用对象**


默认情况下是没有弱引用的：  
```python
class Person:
    pass

p = Person()
print(p.__weakref__)
```
运行结果：
```
None
```
程序说明：普通对象在默认情况下是不存在弱引用的。

下面我们就介绍一下弱引用常见的两种形式
#### 4.1 `weakref.ref(p_object)`方法
使用`weakref.ref`方法是最常规的方法，返回的是一个`weakref`类型对象，如果需要只用引用,需要通过`()`才能获取到被引用对象。
示例代码：
```python
import weakref
import sys


class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age


def ref_callback(reference):
    print("ref_callback")
    print(reference, "this weak reference invalid")


p1 = Person("聂发俊", 100)
print(sys.getrefcount(p1))


# 使用weakref.ref方法构建弱引用
wek1 = weakref.ref(p1, ref_callback)
print(sys.getrefcount(p1))
print("--" * 20)

print(p1.__weakref__)
print(wek1)
print(wek1())
print("{0}的年龄是:{1}".format(wek1().name, wek1().age))

print('--' * 20)
del p1
print(wek1)
```
运行结果：
```
2
2
----------------------------------------
<weakref at 0x00000260D5978CC8; to 'Person' at 0x00000260BE779908>
<weakref at 0x00000260D5978CC8; to 'Person' at 0x00000260BE779908>
<__main__.Person object at 0x00000260BE779908>
聂发俊的年龄是:100
----------------------------------------
ref_callback
<weakref at 0x00000260D5978CC8; dead> this weak reference invalid
<weakref at 0x00000260D5978CC8; dead>
```
程序说明： 
1. 第一部分,两部分的引用数目都是2(为什么是2，可以看前面的垃圾回收机制)，说明弱引用不会增加引用计数。
2. 创建弱引用以后，被引用对象的`__weakref__`也有值了，指向和`wek1`一样的地址。`wek1`是一个`weakref`类型的对象，只有使用`wek1()`的方式，才能获取被引用对象`__main__.Person`,使用被引用对象的实例属性，也需要带上`()`
3. 当删除被引用对象是，会触发构建弱引用的指定的回调函数`ref_callback`,同时`wek1`的弱引用状态变成`dead`状态。  


#### 4.2 `weakref.proxy(p_object, callback)`方法
使用`weakref.proxy`方法创建弱引用返回被引用对象，类似于`a=b`操作，这个是和使用`ref`函数不一样的地方。
示例代码：
```python
import weakref
import sys


class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age


def proxy_callback(reference):
    print("proxy_callback call")


p1 = Person("聂发俊", 100)
print(sys.getrefcount(p1))


# 使用weakref.proxy方法构建弱引用
wek2 = weakref.proxy(p1, proxy_callback)
print(sys.getrefcount(p1))
print("--" * 20)

print(p1)
print(wek2)
print("{0}的年龄是:{1}".format(wek2.name, wek2.age))

print('--' * 20)
del p1
print(wek2)
```
运行结果:
```
2
2
----------------------------------------
<__main__.Person object at 0x0000010E9784F388>
<__main__.Person object at 0x0000010E9784F388>
聂发俊的年龄是:100
----------------------------------------
proxy_callback call
Traceback (most recent call last):
  File "test.py", line 30, in <module>
    print(wek2)
ReferenceError: weakly-referenced object no longer exists
```
程序说明：
1. 引用计数的数目同上面的例子
2. 使用`weakref.proxy`返回的被引用对象，`p1`和`wek2`指向相同的地址`0x000001EFF7219908`,使用实例属性不需要`()`
3. 当删除被引用对象`p1`，方法`weakref.proxy`指定的回调函数`proxy_callback`会被调用，注意**因为被引用对象先被删除，然后在执行回调函数，这个时候虽然有参数reference代指被引用对象，但是不能使用，因为已经被删除了**
4. 这个时候打印`wek2`会提示错误信息，因为被引用对象`p1`被删除了，无法获取对象


#### 4.3 弱引用简单总结
1. `weakref.ref()`返回`weakref`，使用被引用对象需要使用`()`进行辅助，删除被引用对象时，`weakref`状态变成`dead`.
2. `weakref.proxy()`返回被引用对象，可以直接使用被引用对象，删除被引用对象时，`proxy`不能再继续使用，无法再获取被引用对象。
3. 目前在实际开发中没有使用到弱引用，具体使用场景等后面有具体使用场景再补充。



---
> 备注：   
> 更多精彩博客，请访问:[聂发俊的技术博客](http://www.niefajun.com/)  
> 对应视频教程，请访问:[python400](https://www.bilibili.com/video/BV1WE411j7p3)  
> 完整markdown笔记，请访问: [python400_learn_github](https://github.com/niefajun/python400_learn)






