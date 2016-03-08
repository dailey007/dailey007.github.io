---
layout: post
title: python的FileObject模块使用总结
comments: true
categories: python
---

### 问题1：Python换行符问题：\r\n还是\n？

节选自一个知乎上的回答

> 作者：龚奇源 - 链接：[clickme](https://www.zhihu.com/question/19751023/answer/19909758) - 来源：知乎

这是一个很经典的问题。因为不同系统下默认的换行符不同。字符处理时候，这样的“不同”会带来很大的问题，例如line[-2]和line.strpi()会因为平台不同返回不同的值。

解决方法:

+ Python 2

（PEP 278 -- Universal Newline Support，感谢毕勤的补充）：

1. **如果不是txt文件，建议用wb和rb来读写。通过二进制读写，不会有换行问题。**
2. **如果需要明文内容，请用rU来读取（强烈推荐），即U通用换行模式（Universal new line mode）。该模式会把所有的换行符（\r \n \r\n）替换为\n。只支持读入，但是也足够了。这是Python 提供给我们的最好的选择，没有之一。**

对比r和rU的结果：

    content = file(fn, 'r').read()  #test\r\ntest2,这里的换行会因不同系统而不同                
    content = file(fn, 'rU').read()  #test\ntest2,#所有的换行都被统一，不分系统

+ Python3

(Open函数: 2. Built-in Functions，感谢林诚的补充)
请注意：Python3不推荐用rU模式！

```
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True)
```

在Python3，可以通过open函数的newline参数来控制Universal new line mode：读取时候，不指定newline，则默认开启Universal new line mode，所有\n, \r, or \r\n被默认转换为\n ；写入时，不指定newline，则换行符为各系统默认的换行符（\n, \r, or \r\n, ），指定为newline='\n'，则都替换为\n（相当于Universal new line mode）；不论读或者写时，newline=''都表示不转换。

### 问题2： 使用readline(),还是next()?

**readline()与next()混合使用不能正常工作**

+ next()
next()方法当一个文件被用作迭代器，典型例子是在一个循环中被使用，next()方法被反复调用。此方法返回下一个输入行，或引发StopIteration异常EOF时被命中。

+ readline([size])
readline()方法从文件中读取一整行。尾部的换行符保持在字符串中。如果设置size参数且非负，那么一个最大字节数，包括结尾的换行和不完整的行可能会返回。

> **ReadLine()和 next()方法共同使用时，将无法正常工作** ，seek()将文件重新定位到一个绝对位置将刷新预读缓冲器。

如果你把一个file当做一个iterator使用的话，是可以使用使用next()方法的，通常可以放在循环中。会返回下一行或者抛出一个StopIteration异常。 

从网上抄了一段代码：

	#!/usr/bin/python 
	# Open a file 
	fo = open("foo.txt", "r") 
	print "Name of the file: ", fo.name 
	# Assuming file has following 5 lines 
	# This is 1st line 
	# This is 2nd line 
	# This is 3rd line 
	# This is 4th line 
	# This is 5th line 
	for index in range(5): 
		line = fo.next() 
		print "Line No %d - %s" % (index, line) 
	#Close opend file 
	fo.close() 


并且，当时使用`for line in file:`这种用法的时候实际上就是调用的next方法。 

### file模块函数介绍

    os.mknod("test.txt")        创建空文件
    fp = open("test.txt",w)     直接打开一个文件，如果文件不存在则创建文件
    #关于open 模式：
    #w     以写方式打开，
    #a     以追加模式打开 (从 EOF 开始, 必要时创建新文件)
    #r+     以读写模式打开
    #w+     以读写模式打开 (参见 w )
    #a+     以读写模式打开 (参见 a )
    #rb     以二进制读模式打开
    #wb     以二进制写模式打开 (参见 w )
    #ab     以二进制追加模式打开 (参见 a )
    #rb+    以二进制读写模式打开 (参见 r+ )
    #wb+    以二进制读写模式打开 (参见 w+ )
    #ab+    以二进制读写模式打开 (参见 a+ )
    fp.read([size])     #size为读取的长度，以byte为单位
    fp.readline([size]) #读一行，如果定义了size，有可能返回的只是一行的一部分
    fp.readlines([size])#把文件每一行作为一个list的一个成员，并返回这个list。其实它的内部是通过循环调用readline()来实现的。如果提供size参数，size是表示读取内容的总长，也就是说可能只读到文件的一部分。
    fp.write(str)       #把str写到文件中，write()并不会在str后加上一个换行符
    fp.writelines(seq)  #把seq的内容全部写到文件中(多行一次性写入)。这个函数也只是忠实地写入，不会在每行后面加上任何东西。
    fp.close()          #关闭文件。python会在一个文件不用后自动关闭文件，不过这一功能没有保证，最好还是养成自己关闭的习惯。  如果一个文件在关闭后还对其进行操作会产生ValueError
    fp.flush()          #把缓冲区的内容写入硬盘
    fp.fileno()         #返回一个长整型的”文件标签“
    fp.isatty()         #文件是否是一个终端设备文件（unix系统中的）
    fp.tell()           #返回文件操作标记的当前位置，以文件的开头为原点
    fp.next()           #返回下一行，并将文件操作标记位移到下一行。把一个file用于for … in file这样的语句时，就是调用next()函数来实现遍历的。
    fp.seek(offset[,whence])              
    #将文件打操作标记移到offset的位置。这个offset一般是相对于文件的开头来计算的，一般为正数。但如果提供了whence参数就不一定了，
    #whence可以为0表示从头开始计算，1表示以当前位置为原点计算。2表示以文件末尾为原点进行计算。需要注意，如果文件以a或a+的模式打开，每次进行写操作时，文件操作标记会自动返回到文件末尾。
    fp.truncate([size]) #把文件裁成规定的大小，默认的是裁到当前文件操作标记的位置。如果size比文件的大小还要大，依据系统的不同可能是不改变文件，也可能是用0把文件补到相应的大小，也可能是以一些随机的内容加上去。

