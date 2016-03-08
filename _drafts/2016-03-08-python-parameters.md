---
layout: page
title: python小知识汇总
comments: true
---

### 参数的解包和打包
注意收集参数和参数解包形式上很相似，要注意区别。
__收集函数是，在形参中(定义函数)，利用*,**来实现__
__参数解包，则是在实参中(调用函数)，利用*,**来实现__，换言之，在其余场景是不能使用参数解包的。


def demo(a,b,c):
    print(a,b,c)
arg = (1,2,3)
demo(arg)
"""
Traceback (most recent call last):
...
TypeError: demo() takes exactly 3 arguments (1 given)
"""
demo(*arg)
"""
(1, 2, 3)
"""

*arg可以解包元组，而**arg则可以解包字典
参考以下程序

def demo(a,b,c):
    print(a,b,c)
arg = {'a':4,'b':5,'c':6}
demo(**arg)
#(4, 5, 6)

### 可变类型与不可变类型
http://my.oschina.net/lionets/blog/204164

Python 的内建标准类型有一种分类标准是分为可变类型与不可变类型：

可变类型：列表、字典
不可变类型：数字、字符串、元组
因为变量保存的实际都是对象的引用，所以在给一个不可变类型（比如 int）的变量 a 赋新值的时候，你实际上是在内存中新建了一个对象，并将 a 指向这个新的对象，然后将原对象的引用计数 –1.

比如下面的示例：

>>> id(1),id(2)
(507098784, 507098816)
>>> a = 1
>>> id(a)
507098784
>>> a = 2
>>> id(2)
507098816
这里的第一步显示 1 和 2 的 id 就已经在内存中保存了这两个对象，随后给 a 赋不同的值，也只是在改变它的引用而已。

但列表就不同：

>>> b = [0]
>>> id(b)
171805000
>>> b[0] = 1
>>> id(b)
171805000
看起来虽然 b 的 id 没有变，其值却变化了。真的是这样吗？

>>> b = [0]
>>> id(b[0])
507098752
>>> b[0] = 1
>>> id(b[0])
507098784
其实只是一个嵌套引用啦，容器类型管理着对复数个元素的引用，这些元素可以是可变类型也可以是不可变类型，对于不可变类型的元素，当你改变他的值的时候，所发生的事情和最前面举得例子是一样的。另外注意不要简单将容器类型与可变类型划等号，因为还有一个“元组”特例。

变量类型是否可变有一个很重要的应用之处就是作为定义函数的默认参数的时候，形如 def foo(attrs=(1,2)): return 之类。这里设定容器类型的默认参数 attrs 使用了元组而不用列表的原因在于：列表作为一种可变类型非常的不靠谱。当脚本执行到函数定义之处的时候，解释器会对参数表达式做一次“预演算”，并把值保存到内存之中，之后每次调用这个函数的时候，都不会再重新运算其参数表达式，而是直接从“预演算”的结果处取值（引用）。所以如果你的默认参数写了一个列表进去，那么每次你调用这个函数时对这个列表所做的更改都会被保存下来。就像这样：


>>> def biggest(n, store=[0]):
     store[0] = max(n, store[0])
     return store[0]
 
>>> biggest(3)
3
>>> biggest(9)
9
>>> biggest(5)
9
这是一个返回历史最大值（仅限正数，因为我不知道该怎么表示极小值）的函数，这个函数没有使用全局变量，却可以在重复调用的时候记录下曾输入过的最大值，靠的就是这个 store=[0] 的默认参数。这个参数的值 [0] 在函数定义的时候就被写入内存之中了，之后每次调用都会引用那个地址，所以是可以当一个全局变量一样使用，却比直接定义的全局变量安全。

其实我想说的是，一般情况下不要用列表做参数，而是用元组。当然除非你知道自己在干什么，否则这只会带来麻烦。上面举了一个正面的例子是因为我看到这个话题的地方就是拿这个特性来做一个 trick 在用。他实现的是一个简单的 timer，每一次调用这个计时函数，都会打印出自上一次调用起到本次调用所经过的时间：


import time
def dur( op=None, clock=[time.time()] ):
    if op != None:
        duration = time.time() - clock[0]
        print '%s finished. Duration %.6f seconds.' % (op, duration)
    clock[0] = time.time()
 
if __name__ == '__main__':
    import array
    dur()   # Initialise the timing clock
     
    opt1 = array.array('H')
    for i in range(1000):
        for n in range(1000):
            opt1.append(n)
    dur('Array from append')
     
    opt2 = array.array('H')
    seq = range(1000)
    for i in range(1000):
        opt2.extend(seq)
    dur('Array from list extend')
     
    opt3 = array.array('H')
    seq = array.array('H', range(1000))
    for i in range(1000):
        opt3.extend(seq)
    dur('Array from array extend')
上例来自于 http://code.activestate.com/recipes/578776-a-simple-timing-function/，并且是 Python2 版本

这个函数里除了用于计时的 clock=[time.time()] 参数外，还有一个用于打印的表示当前 __main__ 函数进度的 op 字符串参数。他的函数里设计了一个逻辑：如果调用时没有给出 op 参数，那么就认为这次调用是在初始化，就像 __main__ 函数里第二行那样。其实我觉得这样不太好，因为完全可以改成：


import time
def dur(op='Undefined',clock=[time.time()]):
     duration = time.time() - clock[0]
     print('%s finished\t Duration:%.6f seconds.'%(op,duration))
     clock[0] = time.time()
 
if __name__ == '__main__':
    import array
    dur('Initialise')   # Initialise the timing clock
这样直接通过 op='Initialise' 来显式初始化计时器更直观一些，而且可以避免在已经初始化后因为忘记给出 op 参数而造成计时错误的可能，虽然可能性很小。

###python参数传递

和其他语言不一样，传递参数的时候，python不允许程序员选择采用传值还是传引用。Python参数传递采用的肯定是“传对象引用”的方式。实际上，这种方式相当于传值和传引用的一种综合。如果函数收到的是一个可变对象（比如字典或者列表）的引用，就能修改对象的原始值－－相当于通过“传引用”来传递对象。如果函数收到的是一个不可变对象（比如数字、字符或者元组）的引用，就不能直接修改原始对象－－相当于通过“传值'来传递对象。

python一般内部赋值变量的话，都是传个引用变量，和C语言的传地址的概念差不多。可以用id()来查询内存地址

如果a=b的话， a和b的地址是相同的；如果只是想拷贝，那么就得用 a=b[:]。
