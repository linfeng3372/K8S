### 一：函数语法

def + 函数名 + (参数): pass

1:函数参数

#### 1.1：无参函数

```python
def func(): pass
```

#### 1.2：带参数函数

```python
def func(arg1, arg2): pass
def mysub(x, y):
    return x - y
m = 10;n = 5
print('{}-{}={}'.format(m,n, mysub(m, n)))

#使用关键字参数方式调用，传参时候指定x与y的值
m = 10;n = 5
ret = mysub(y=n, x=m)
print('{}-{}={}'.format(m,n, ret))

#关键字参数后面不能有非关键字参数

```

#### 1.3：默认值参数

```python
def func(x, y =1):pass
#带默认值参数后不能有无默认值参数

```

#### 1.4：可变长参数

```python
两个函数print和dict
1：使用print函数打印信息：print(val1, val2, val3...)
	#print使用可变长非关键字参数
2: 创建字典一种方式: dict(x=val1, y = val2, z = val3 ...)
    #dict使用可变长关键字参数
    
可变长非关键字参数定义
def func(*arg): print(arg, type(arg))
    #*arg 可以理解为它将传递的参数收集到元组中
    
	func(1,2,3) ---(1,2,3---(1,2,3))----> def func(*arg): print(arg)
        
可变长关键字参数定义
def foo(**kwargs): print(kwargs, type(kwages))
    #**kwargs 作用将关键字参数收集到字典中
    
```

```python
def countprice(nums, *coupon,discount=0.9, **booksinfo,):
    print(booksinfo.values(), booksinfo.keys())
    print(list(booksinfo.values()))
    price = sum(list(booksinfo.values()))
    allprice = price * nums
    discountprice = allprice * discount
    realprice = discountprice - sum(coupon)

    return realprice

ret1 = countprice(1,1,2,3,discount=0.8,book1=58,book2=90)
print(ret1)
```

### 二：命名空间与作用域

#### 2.1:命名空间

命名空间是一个字典，键为变量名，值是对应的值；函数有自己命名空间，函数外部有全局命名空间

各个命名空间是独立没有关系的，同一个命名空间中变量名不能重复

不同的命名空间可以重名而不相互影响

#### 2.2:作用域

python作用域分类LEGB

```
L：Local,函数内作用域

E：Enclosing，外部嵌套函数作用域

G：Global，全局作用域；

B：Builtin，Python内置模块作用域
```

python中变量查找遵循LEGB原则

```
命名空间：定义作用域内变量名和绑定值之间的对应关系

作用域：命名空间中的变量作用范围

变量查找过程：当前命名空间没有去找上一层
```

```python
a = 10 #Global
def func(a, val):
    #局部变量
    a+=10
    print('in size func a=', a)

func(a, 10)
print('outside func a=', a)

#在函数内部，我们修改的局部变量a，而不是全局变量a
```

### 三：返回值

函数使用return返回返回值，默认返回值为None

代码执行到return后直接返回，不再执行函数中的其他语句

```python
def func():
    pass
print('func()返回值', func())
#返回None
```

返回多个值

```python
####################
import random
def countscore(*args):
    saverage = sum(args)/len(args)
    smax = max(args)
    smin = min(args)
    return saverage, smax, smin

listsocre = []
for i in range(20):
    listsocre.append(random.randint(50,100))

result = countscore(*listsocre)
print('result', result)
#多元复制
average, smax, smin = result
print(average, smax, smin)
```

### 四：函数相关要点

#### 4.1：作用域陷阱

函数外部定义全局变量a,func函数对a进行修改，Python解释器认为a为局部变量

但局部变量a没有初始化，Python不知道如何处理，抛出异常

```python
a = 10
def func(val):
    b = a + val
    print(b)
func(10)


def func2(val):
    a = a + 10

func2(10)
```

#### 4.2：关键字global

```python
a = 10
def func2(val):
    global a
    a = a + 10
func2(10)
```

### 五：内嵌函数与闭包

#### 5.1：内嵌函数

##### 1：内嵌函数是指在函数内部定义函数

```python
def func():
    print('i am in func() out foo()')
    def foo():
        print('i am in foo()')
func()
#i am in func() out foo()
```

调用foo函数

1：调用func()返回值foo函数，f指向foo函数

2：调用f()执行foo()函数

```python
def func():
    print('i am in func() out foo()')
    #内嵌函数
    def foo():
        print('i am in foo()')
    #返回foo函数对象
    return foo
#指向foo函数
f = func()
print(f)
f()

#结果
i am in func() out foo()
<function func.<locals>.foo at 0x000000000290D4C8>
i am in foo()
```

##### 3：给函数加上参数

```python
def func(base):
    #print('i am in func()')
    def foo(val):
        #print('i am in foo()')
        return val * base
    return foo
f1 = func(5)
f2 = func(10)

print(f1(2), f2(2))

print(f1.__closure__)
print(f2.__closure__)
print('0x%x,0x%x' %(id(5), id(10)))
func(5)调用过程,base的值为5，foo函数中使用base；
当func执行结束时base会被销毁，foo函数怎么办？
foo函数作为返回值被复制给变量f1，foo没有被销毁
base在哪里，base处于两个函数之间，Enclosing 作用域登场；
base放在哪里了？

base放在函数'closure'属性中
base对于foo函数来说就是'Enclosing'作用域变量
这里我们成为foo函数为闭包,闭包就是内置函数引用Enclosing作用域变量
```

#### 5.2：闭包应用

```python
def genLogFunc(level):
    def logfunc(msg):
        print('{}!msg:{}'.format(level, msg))
    return logfunc

logError = genLogFunc('Error')
logWarning = genLogFunc('Warning')
logError('test')
logWarning('test')
```

### 六：装饰器

#### 6.1：装饰器是什么？

​	装饰器是Python的语法糖，实质是一个闭包使用

​	装饰器实质是修改函数，将函数中相同功能类似的代码抽取出来统一处理，使得代码更加简洁清晰

​	例如：函数参数检查，日志处理，权限检查等

```python
@deco func():pass
```

#### 6.2：实现过程f分析

```python
def checkvalid(userid,pwd):
    return True

def inserValue(userid, pwd, **kwargs):
    if not check(userid, pwd):
        return False

def queryValue(userid, pwd, **kwargs):
    if not check(userid, pwd):
        return False


#考虑使用闭包，增加一个函数，函数参数为函数，如下
def checkargs(func):
    def foo(userid, pwd, **kwargs):
        if not checkvalid(userid, pwd):
            return
        func(userid, pwd, **kwargs)
    return foo

"""
1>checkargs参数为函数，具体做什么我们不关心
2>内置函数foo的参数要符合func的参数要求，最好保持一致
3>foo函数，检查参数，如果userid与pwd无效直接返回，否则调用func函数
4>checkargs返回值为foo函数，foo函数为闭包
"""

```

#### 6.3：装饰器调用

```python
def checkvalid(userid,pwd):
    return True



def inserValue(userid, pwd, **kwargs):
    pass

def queryValue(userid, pwd, **kwargs):
    pass


#定义检查参数函数
#输入参数为函数，返回值为函数
def checkargs(func):
    print('call checkargs')
    def foo(userid, pwd, **kwargs):
        print('call here foo function')
        if not checkvalid(userid, pwd):
            return
        func(userid, pwd, **kwargs)
    return foo


#insertValue直接插入
#@checkargs 为装饰器语法糖，功能理解为：inserValue = checkargs(inserValue)
#装饰器会调用函数，重新修改现有函数，返回新的函数


#@checkargs
def inserValue(userid, pwd, **kwargs):
    print('call here inserValue')

# @checkargs
# def queryValue(userid, pwd, **kwargs):
#     print('call here queryValue')

#inserValue = foo
inserValue = checkargs(inserValue)

#foo()
inserValue('test1', '123456')

首先调用foo函数检查参数，如果参数有效，调用inserValue函数，否则直接返回
```

#### 6.4：理解装饰器

```
装饰器是一个函数，用于装饰函数
装饰器返回一个新的函数
主要作用是对已存在韩硕或者对象增加新的功能
```

#### 6.5：不同类型装饰器

##### 6.5.1：装饰不带参数函数

```python
def deco(func):
    print('deco func:', func)
    def foo():
        print('before call testfunc')
        print('func info:', func)
        func()
        print('after call testfunc')
    return foo

@deco
def testfunc():
    print('call testfunc()')

testfunc()
```

##### 6.5.2：装饰带参数函数

```

```

##### 6.5.3：装饰器参数

```python
def decoLog(level):
    print('call decoLog')
    def deco(func):
        print('call deco')
        def logFunc(msg):
            print('%s:'%level, end='')
            func(msg)
        return logFunc
    return deco

#decoLog('Error')返回deco函数
#deco函数装饰LogError
@decoLog('Error')
def LogError(msg):
    print('%s'%(msg))

@decoLog('Debug')
def LogDebug(msg):
    print("%s"%(msg))

LogError('this is test')
LogDebug('this is test')
```



### 七： 匿名函数

匿名函数：没有名称函数，Python中使用lambda关键字定义

基本语法：`lambda + arg(参数): pass`

匿名函数返回值：函数对象

作用：省去定义过程，使代码简单

注意点：匿名函数只是一条表达式语句，返回值为表达式运行结果

#### 7.1:一个参数匿名函数

```python
isEven = lambda x: x%2 == 0
#isEven为函数，返回值为x%2 == 0表达式值
print('{} is event.{}'.format(1, isEven(1)))
print('{} is event.{}'.format(2, isEven(2)))
```

#### 7.2：多个参数匿名函数

```python
myadd = lambda x,y,z: x+y+z
myadd(1,2,3)
print(myadd(1,2,3))
```

#### 7.3：带默认参数例子

```python
phoneprice = lambda price, coupon = 0 : price - coupon
print('不使用优惠券:', phoneprice(1499))
print('使用优惠券:', phoneprice(1499,100))
```

#### 7.4：可变长参数非关键字

```python
func = lambda **kwargs:sum(list(kwargs.values()))/len(kwargs)

score = {'1':90, '2':100, '3':80}
print(func(**score))
```

### 八：递归函数

递归函数实质是自己调用自己，递归函数的一个要点是递归结束条件

```python
def factorial(n):
    if n == 1:
        return n
    return n * factorial(n-1)
```


