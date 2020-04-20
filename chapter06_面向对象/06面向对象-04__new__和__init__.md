## python-06面向对象-04构造函数以及初始化

### 1.引题
类是抽象的，也称之为“对象的模板”。我们需要通过类这个模板，创建类的实例对象，然后才能使用类定义的功能。

我们前面说过的一个Python对象包含三个部分：  

1. id(identity识别码)  
2. type(对象类型)  
3. value(对象的值)

现在，我们可以更进一步的说，一个python对象包含如下的部分:

1. id(identity识别码，一般是存储地址)   
2. type(对象类型)
3. value(对象的值)
	* 属性(attribute)
	* 方法(method)

创建对象，会首先调用`__new__()`构造方法，然后执行`__init__()`初始化方法。构造方法创建对象，初始化方法用于执行“实例对象的初始化工作”，即对象创建后，初始化当前的相关属性，无返回值。

### 2.官方说明
#### 2.1 源码注释说明
```python
class object:
    def __init__(self): # known special case of object.__init__
    """ Initialize self.  See help(type(self)) for accurate signature. """
    pass
	        
	@staticmethod # known case of __new__
	def __new__(cls, *more): # known special case of object.__new__
	    """ Create and return a new object.  See help(type) for accurate signature. """
	    pass
```
通过根基类object中源码定义的方法的注释信息可以知道：

1. `__new__()`方法:创建并返回一个新对象
2. `__init__()`方法: 初始化self，self代指传入的对象

> 此处源码指的PyCharm中用于提示代码信息的源码信息，非实际源码，结构基本结构，缺少具体实现。

#### 2.2 实例代码说明
```python
class Student(object):
    def __new__(cls):
        print("__new__方法被调用")
        return super().__new__(cls)

    def __init__(self):
        print("__init__方法被调用")
```

执行结果：
> `__new__方法被调用`  
> `__init__方法被调用` 

通过官方源码以及实例信息，我们大概知道：首先通过`__new__()`方法首先来创建对象，然后使用`__init__()`方法来进行初始化操作。
> 具体完整的详细信息，后面的博客会进行详细说明



### 3.初始化方法要点说明

`__init__()`的要点如下:  

1. 名称固定，必须为：`__init__()` 
2. 第一个参数固定，约定俗成为:self,代指刚刚创建好的实例对象。实际上参数名可以任意修改，不影响使用，但是影响代码的可读性，所以遵循惯例。

	```python
	class Student:
	    def __init__(s, name, age):
	        """
	        使用s代指self,在pyCharm会提示改进信息，但是可以正常执行
	        """
	        s.name = name
	        s.age = age

	
	s1 = Student("聂发俊", 100)
	print(s1.name)
	print(s1.age)
	```
	
	执行结果
	
	```text
	聂发俊  
	100
	```
	
 ---
 > **补充说明：Python中self相当于C++中的self指针，Java和c#中的this关键字，Python中self必须为构造函数的第一个参数。**  
 
 ---


3. 构造函数通常用来初始化实例对象的实例属性。

	```python
	class Student:
	    def __init__(self, name, age):
	        """
	        初始化实例属性
	        """
	        self.name = name
	        self.age = age
	```
	
4. 通过“类名(参数列表)”来调用构造方法。调用后，将创建号的对象返回给相应的变量。

	```python
	s1 = Student("聂发俊"， 100)
	```
	通过上面的介绍，实际上这里调用了2个方法。




---
>  
> 备注：   
> 更多精彩博客，请访问:[聂发俊的技术博客](http://www.niefajun.com/)  
> 完整markdown笔记，请访问: [python400_learn_github](https://github.com/niefajun/python400_learn)