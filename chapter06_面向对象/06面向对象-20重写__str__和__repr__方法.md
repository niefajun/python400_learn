## python-面向对象-20-重写`__str__`方法/`__repr__`方法

### 1.基本介绍
object类有一个`__str__()`方法，用于返回一个"对象的描述"，对应于内置函数`str()`，经常用于`print()`方法，帮助我们查看对象的信息。
因为`__str__()`默认在object类中定义，所以子类可以重写这个方法。  

不同语言中对应的不同的方法名：
1. Python语言: `__str__()`方法
2. JAVA语言：`toString()`方法
3. PHP语言：`__toString()`方法

### 2.方法定义
`__str__()`默认定义在类`object`当中。

```python
def __str__(self, *args, **kwargs): # real signature unknown
        """ Return str(self). """
        pass
```

### 3.简单示例
示例代码：
```python
class Student(object):
    pass

class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return "名字:{0},年龄:{1}".format(self.name, self.age)

s1 = Student()
print(s1)
print("--" * 20)

p1 = Person("聂发俊", 100)
print(p1)
```
运行结果：
```
<__main__.Student object at 0x0000015E99B5F3C8>
----------------------------------------
名字:聂发俊,年龄:100
```
程序说明：
1. 类Student直接继承类object，所以`__str__()`也直接沿用，所以打印内容格式如下：`<模块名.类名 被继承类名 at 内存地址>`的格式
2. 类Person继承类object，但是定义了`__str__()`，属于子类重写父类方法，采用子类的`__str__()`方法，所以执行打印的时候，输出：`名字:聂发俊,年龄:100`.


### 4.`__str__`和`__repr__`区别
`str()`函数和`__str__`在日常的开发看的很多，但是可能很少人会注意`__repr__`,这两个很接近，但是有一些细微的差别。  
相同点： 两个都是打印输出功能的函数  
不同点： `__str__`是面向用户显示的，在IDE和命令行都出现，很常见。`__repr__`是面向程序显示，只出现在命令行，很少见。
示例代码：
```python
>>> class Person:
...     def __init__(self, name, age):
...             self.name = name
...             self.age = age
...     def __str__(self):
...             return "__str__:{0},{1}".format(self.name, self.age)
...     def __repr__(self):
...             return "__repr__:{0},{1}".format(self.name, self.age)
...
>>> p1 = Person("niefajun", 100)
>>> p1
__repr__:niefajun,100
>>> print(p1)
__str__:niefajun,100
```
程序说明：
1. 在命令行环境中，定义类`Person`，包含两个实例属性`name`和`age`,另外重写了`__str__(self)`和`__repr__(self)`两个方法，并且实例化对象p1
2. 当单独执行`>>> p1`的时候，由于在命令行环境，这个时候会执行`__repr__(self)`，完成打印功能，所以显示：`__repr__:niefajun,100`,而当使用`print(p1)`相当于是给用户看的，会调用`__str__(self)`方法，所以输出：`__str__:niefajun,100`。

> 注意：**在python文件中使用的都是`__str__`**



---
> 备注：   
> 更多精彩博客，请访问:[聂发俊的技术博客](http://www.niefajun.com/)  
> 对应视频教程，请访问:[python400](https://www.bilibili.com/video/BV1WE411j7p3)  
> 完整markdown笔记，请访问: [python400_learn_github](https://github.com/niefajun/python400_learn)
