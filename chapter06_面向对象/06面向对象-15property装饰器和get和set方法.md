## python-面向对象-15-property装饰器/get和set方法

### 1.@property装饰器
`@property`可以将一个方法的调用方式变成"属性调用"。下面是一个简单的示例，让大家体会一下这种转变。
示例代码：

```python
class Employee:

    @property
    def salary(self):
        return 30000

emp1 = Employee()
print(emp1.salary)
print(type(emp1.salary))
emp1.salary()
```
运行结果：

```python
Traceback (most recent call last):
  File "test.py", line 11, in <module>
    emp1.salary()
TypeError: 'int' object is not callable
30000
<class 'int'>
```
代码说明： 

1. 首先实例方法salaly使用装饰`@property`修饰，从普通实例方法转变成通过"实例属性"调用的特殊实例方法。使用`print(emp1.salary)`,本质是调用对象emp1的salary实例方法，返回值30000,所以第一个打印30000.
2. 30000是int类型的数值，所以使用type函数，显示`<class 'int'>`
3. 使用普通的调用方式去调用装饰器`@property`修饰的方法，结果报错，说明：实例方法一旦使用装饰器`@property`修饰，就不能再使用`对象名.实例方法名([形参列表])`的方式调用。



### 2.get和set方法

#### 2.1 普通意义上的get和set方法
示例代码:

```python
class Employee:

    def __init__(self, name, salary):
        self.__name = name
        self.__salary = salary

    def get_salary(self):
        return self.__salary

    def set_salary(self, salary):
        if 1000 < salary < 50000:
            self.__salary = salary
        else:
            print("录入错误!薪水在1000~50000这个范围")


emp1 = Employee("聂发俊", 2000)
print(emp1.get_salary())
emp1.set_salary(3000)
print(emp1.get_salary())
```

运行结果：

```python
2000
3000
```
代码说明：  
1. 因为在实例化对象的时候，设置的`salary`值为2000，所以打印的时候也是2000.
2. 首先对`salary`属性设置值3000，因为值范围在(1000, 50000)之间，所以可以正常设置，所以打印的时候，也是3000。


#### 2.2 使用装饰器实现get和set方法

示例代码:

```python
class Employee:

    def __init__(self, name, salary):
        self.name = name
        self.__salary = salary


    @property
    def salary(self):
        return self.__salary

    @salary.setter
    def salary(self, salary):
        if 1000 < salary < 50000:
            self.__salary = salary
        else:
            print("录入错误!薪水在1000~50000这个范围")


emp1 = Employee("聂发俊", 2000)
print(emp1.salary)
emp1.salary = 3000
print(emp1.salary)
```

运行结果：

```python
2000
3000
```
程序说明：
1. 这个例子和上个例子是功能相同的代码，只是采用的写法不一致而已。`@property`代表获取属性，`@salary.setter`代表设置属性，分别对应`get_salary`和`set_salary`方法。



---
> 备注：   
> 更多精彩博客，请访问:[聂发俊的技术博客](http://www.niefajun.com/)  
> 对应视频教程，请访问:[python400](https://www.bilibili.com/video/BV1WE411j7p3)  
> 完整markdown笔记，请访问: [python400_learn_github](https://github.com/niefajun/python400_learn)


