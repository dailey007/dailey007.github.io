---
layout: post
title: python的time模块使用总结
comments: true
categories: python
---

在python中，经常使用time模块，但在使用过程中，各个函数的关系及相互转换，总是一头雾水，找时间总结了下。

### time 模块中时间表示形式
* 时间戳： 相对于1970.1.1 00:00:00以秒计算的偏移量
* 字符串： 以字符串的形式表示时间，如“Sat Mar 28 22:24:24 2009”
* 数组：即struct_time,共九个元素，如下所示
	+ year (four digits, e.g. 1998)
	+ month (1-12)
	+ day (1-31)
	+ hours (0-23)
	+ minutes (0-59)
	+ seconds (0-59)
	+ weekday (0-6, Monday is 0)
	+ Julian day (day in the year, 1-366)
	+ DST (Daylight Savings Time) flag (-1, 0 or 1) 是否是夏令时

> Attention: 同一个时间戳的struct_time会因为时区不同而不同

### 函数介绍
在介绍函数之前，先看下下面的函数关系图。

![函数关系图](/static/img/python_time_module.png)

#### 1. asctime()
asctime([tuple]) -> string

将一个struct_time(默认为当时时间)，转换成字符串

#### 2. clock()
clock() -> floating point number

该函数有两个功能，在第一次调用的时候，返回的是程序运行的实际时间；以第二次之后的调用，返回的是自第一次调用后,到这次调用的时间间隔。

#### 3. sleep(...)
sleep(seconds)，线程推迟指定的时间运行，经过测试，单位为秒。

#### 4.ctime(...)
ctime(seconds) -> string

将一个时间戳(默认为当前时间)转换成一个时间字符串
例如：`time.ctime()`，输出为：'Sat Mar 28 22:24:24 2009'

#### 5.gmtime(...)  => *将时间戳转换成UTC时区（0时区），返回值为数组时间*
gmtime([seconds]) -> (tm_year, tm_mon, tm_day, tm_hour, tm_min,tm_sec, tm_wday, tm_yday, tm_isdst)

将一个时间戳转换成一个UTC时区(*0时区*)的struct_time，如果seconds参数未输入，则以当前时间为转换标准

#### 6.localtime(...) =》*将时间戳转换成当前时区，返回值为数组时间*
localtime([seconds]) -> (tm_year,tm_mon,tm_day,tm_hour,tm_min,tm_sec,tm_wday,tm_yday,tm_isdst)

将一个时间戳转换成一个当前时区的struct_time，如果seconds参数未输入，则以当前时间为转换标准

#### 7.mktime(...) => *将数组时间转化成时间戳*
mktime(tuple) -> floating point number

将一个以struct_time转换为时间戳

#### 8.strftime(...) => *将数组时间 转换成 格式化字符串*
strftime(format[, tuple]) -> string

将指定的struct_time(默认为当前时间)，根据指定的格式化字符串输出
python中时间日期格式化符号：

* %y 两位数的年份表示（00-99）
* %Y 四位数的年份表示（000-9999）
* %m 月份（01-12）
* %d 月内中的一天（0-31）
* %H 24小时制小时数（0-23）
* %I 12小时制小时数（01-12） 
* %M 分钟数（00=59）
* %S 秒（00-59）
* %a 本地简化星期名称
* %A 本地完整星期名称
* %b 本地简化的月份名称
* %B 本地完整的月份名称
* %c 本地相应的日期表示和时间表示
* %j 年内的一天（001-366）
* %p 本地A.M.或P.M.的等价符
* %U 一年中的星期数（00-53）星期天为星期的开始
* %w 星期（0-6），星期天为星期的开始
* %W 一年中的星期数（00-53）星期一为星期的开始
* %x 本地相应的日期表示
* %X 本地相应的时间表示
* %Z 当前时区的名称
* %% %号本身

#### 9.strptime(...) => *将时间字符串 转化成 数组时间*
strptime(string, format) -> struct_time

将时间字符串根据指定的格式化符转换成数组形式的时间
例如：2009-03-20 11:45:39* 对应的格式化字符串为：%Y-%m-%d %H:%M:%S

#### 10.time(...)
time() -> floating point number

返回当前时间的时间戳

