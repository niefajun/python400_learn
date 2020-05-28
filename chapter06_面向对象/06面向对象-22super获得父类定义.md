## python-面向对象-22-super获得父类定义
### 1.简要介绍

在类的继承关系中，子类想要获得父类的定义的时候，一般会用到`super()`，前面最开始介绍面向对象三大特征的时候，考虑到当时普通的单继承，后面还有多重继承的内容，当时虽然有场景可以引出`super()`，但是并没有，现在在多重继承之后，相对完整的来进行介绍。  

**super()代表父类的定义(注意是类)，不是父类对象。  **

首先来看`super()`定义：
```python
class super(object):
    """
    super() -> same as super(__class__, <first argument>)
    super(type) -> unbound super object
    super(type, obj) -> bound super object; requires isinstance(obj, type)
    super(type, type2) -> bound super object; requires issubclass(type2, type)
    Typical use to call a cooperative superclass method:
    class C(B):
        def meth(self, arg):
            super().meth(arg)
    This works for class methods too:
    class C(B):
        @classmethod
        def cmeth(cls, arg):
            super().cmeth(arg)
    """
```
`super()`在这里是用`class`进行修饰的，说明是一个类，而不是我们通常理解那样是一个方法。同时在后面的注释中介绍`super()`的典型用法，调用父类方法，**包括普通的实例方法，也包含类方法**。



### 2.`super()`应用在实例方法中

最典型的实例方法就是：`__init__()`，主要就是调用父类的`__init__()`方法对参数进行初始化。

示例代码：
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

class Student(Person):
    def __init__(self, name, age, score):
        # 第一种调用方法,手动调用父类初始化方法
        # Person.__init__(self, name, age)

        # 第三种调用方法, 指定类Student, 一般不这么写
        # super(Student, self).__init__(name, age)

        # 第二种调用方法,不指定类, 通用写法
        super().__init__(name, age)
        self.score = score

    def __str__(self):
        return "name={0},age={1},score={2}"\
            .format(self.name, self.age, self.score)

s = Student("聂发俊", 100, 60)
print(s)
```

运行结果：
```
name=聂发俊,age=100,score=60
```

程序说明：  
在程序中，我们使用三种方法，分别来调用父类的实例方法，实现的功能是一样的。  
1. 第一种方法：`Person.__init__(self, name, age)`，直接调用父类Person的`__init__`方法，这里面的`self`是指类`Student`的实例，但是这样破坏了类之间的耦合性，而且在多继承的情况下，需要根据类的多继承关系，自己判断出第一个直接继承关系的类，比较麻烦，不通用，一般不建议。
2. `super()`里面需要的类型是type类型，采用`super(Student)`，会认为是str类型，提示报错。**具体为什么这种方式能够返回type类型，我目前也不是很清楚，欢迎大神指教。**
3. 第三种调用方法，比较常见的写法，让`super()`自动识别，找到父类的`__init__()`方法 



### 2.`super()`应用在类方法中
这里所说的类方法，包括类方法和静态方法。  
示例代码：

```python
class A:
    @classmethod
    def class_method(cls):
        print("A class method", cls)

    @staticmethod
    def static_method():
        print("A static method")
        
class B(A):
    @classmethod
    def class_method(cls):
        # 第一种调用方法
        # super(B, cls).class_method()

        # 第二种调用方法
        super().class_method()

        print("B class method", cls)
        super().static_method()

B.class_method()
```
运行结果：  
```python
A class method <class '__main__.B'>
B class method <class '__main__.B'>
A static method
```
程序说明：  
1. 首先定义类A，其中包括类方法`class_method`和静态方法`static_method`
2. 定义类B，指定继承类A，并且在类B中也定义同名的类方法`class_method`,其实不同名字也可以，在方法中，首先通过`super()`调用的父类的类方法，和前面的原理一致，但是因为调用方法的类是类B，不是类A,所以打印：`A class method <class '__main__.B'>`
3. 然后打印类B对应类方法内容，这个就正常输出：`B class method <class '__main__.B'>`
4. 最后调用类A的静态方法,因为静态方法和类和类属性的关系不大，属于独立方法，所以也是正常执行输出:`A static method`.



### 3.简单总结

`super()`用于获取父类的定义，主要用于调用父类的方法,适用于实例方法、类方法、静态方法。



---
> 备注：   
> 更多精彩博客，请访问:[聂发俊的技术博客](http://www.niefajun.com/)  
> 对应视频教程，请访问:[python400](https://www.bilibili.com/video/BV1WE411j7p3)  
> 完整markdown笔记，请访问: [python400_learn_github](https://github.com/niefajun/python400_learn)
