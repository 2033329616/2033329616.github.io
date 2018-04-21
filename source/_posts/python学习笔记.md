---
title: python学习笔记
tags: 
- Python
categories:
- Python
comments: true
sitemap: false
grammar_cjkRuby: true
---


###  1. python里*args和**kwargs的用法
`引入参数*args和**kwargs，两个都是python中的可变参数。*args表示任何多个无名参数，它是一个tuple`
`**kwargs表示关键字参数，它是一个dict。并且同时使用*args和**kwargs时，必须*args参数列要在**kwargs前`
```python
def foo(*args, **kwargs):
    print 'args = ', args
    print 'kwargs = ', kwargs
    print '---------------------------------------'

if __name__ == '__main__':
    foo(1,2,3,4)                             #参数只传到*args中
    foo(a=1,b=2,c=3)                         #参数只传到**kwargs中
    foo(1,2,3,4, a=1,b=2,c=3)                #参数可以传到*args和**kwargs中
    foo('a', 1, None, a=1, b='2', c=3)
```
运行结果：
```python
args =  (1, 2, 3, 4)
kwargs =  {}
---------------------------------------
args =  ()
kwargs =  {'a': 1, 'c': 3, 'b': 2}
---------------------------------------
args =  (1, 2, 3, 4)
kwargs =  {'a': 1, 'c': 3, 'b': 2}
---------------------------------------
args =  ('a', 1, None)
kwargs =  {'a': 1, 'c': 3, 'b': '2'}
---------------------------------------
```
###  2.python对txt文件的操作
2.1 使用**open()** 函数读取文本内容，本函数是打开一个文件并返回文件对象。如果文件不能打开，抛出异常OSError
`open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=T)`
参数mode是指明打开文件的模式。默认值是’r’，表示使用文本的方式打开文件来读取。

‘r’表示打开文件只读，不能写。

‘w’表示打开文件只写，并且清空文件。

‘x’表示独占打开文件，如果文件已经存打开就会失败。

‘a’表示打开文件写，不清空文件，在文件后尾追加的方式写入。

‘b’表示二进制的模式打开文件。

‘t’表示文本模式，默认情况下就是这种模式。

‘+’打开文件更新（读取或写入）。

缺省时的模式就相当于’rt’。比如’w+b’就是打开文件进入读写，把文件清空；’r+b’打开文件，但不把文件清空
打开文件的操作常使用异常捕捉来

str.strip(rm)方法,去除字符串开头和结尾的rm字符，`readline()`获取的是一行文本的字符串形式
```python
def open_file(filename):
    """打开一个文件，存储到列表中，并返回该列表"""
    try:
        with open(filename) as file_open:    #打开文件，因为只有一行，所以不用使用循环
            data = file_open.readline()
        # [james,james2,james3] = data.strip().split(',', 2)          #方法串链，从左向右作用
        file_data = data.strip().split(',')
        """split方法返回字符串列表，如果只是一个标识符接受，则不会报错
        如果是[list1,list2,...]的形式则有可能因为接受数据的列表元素个数，与返回的
        字符串列表的个数不一致而导致ValueError"""
        return file_data
    except IOError as ioerr:
        print 'IOError:' + str(ioerr)
        return None
    except ValueError as valerr:
        print 'ValueError:' + str(valerr)
        return None
```
 `(role, line_spoken) = each_line.split(' ,')`，多重赋值(),[]均可以接受数据-字符串列表，**each_line本身不会被分割，保持不变**
`[role, line_spoken] = each_line.split(', ',1)`
`[string1, string2, string3] = each_line.split(', ', 2)`如果分割后的字符首位有“空格”，也会把其当成字符串第二个参数表示使用的分割符的个数，使用1则分为两个部分，如果不加则表示只要存在分隔符就会分解,n个分隔符分为n+1个部分，空字符也算入被分割的部分

2.2 使用**open()** 函数和**file_object.write()** 方法写入文本
**方法一、增加额外的逻辑来处理异常**,使用finally来关闭文件对象
```python
try:   #文件写入过程
    man_file = open('man_data.txt', 'w')         #新建文件
    other_file = open('other_data.txt', 'w')
    """ write()与print都可以写入数据，当print可以由字符串或列表写入，
    write()括号里只能是字符串，所以列表需要先转换后字符串再放到括号里"""
    # print >> man_file, man                       #文件对象里传入数据
    # print >> other_file, other
    man_file.write(str(man))
    other_file.write(str(other))
except IOError:
    print 'File error.'
finally:  #无论有无异常，此句都会执行，
    man_file.close()
    other_file.close()
```
**方法二、with处理文件异常**,不用考虑关闭文件对象
```python
try:   #文件写入过程
    # with open('man_data.txt', 'w') as man_file:         #新建文件
    #     # print >> man_file, man                        #文件对象里传入数据
    #     man_file.write(str(man))
    # with open('other_data.txt', 'w') as other_file:
    #     """ write()与print都可以写入数据，当print可以由字符串或列表写入，
    #          write()括号里只能是字符串，所以列表需要先转换后字符串再放到括号里"""
    #     # print >> other_file, other
    #     other_file.write(str(other))
    with open('man_data.txt', 'w') as man_file, open('other_data.txt', 'w') as other_file:
        #两个语句合并为一个with语句
        man_file.write(str(man))
        other_file.write(str(other))
except IOError as err:
    print 'File error:' + str(err)
```
2.3实例
打开train_data.txt，读取其中的前四行，然后存储到train_data2.txt文件里

```python
# encoding:utf-8
# file.readlines()读取数据的行数，'行数据\n' 是该方法输出列表的每行数据的元素
attribute_data = []
try:
	with open('./train_data.txt') as file,  open('./train_data2.txt','w') as file2:
		raw_number = len(file.readlines())      # 计算文件的总行数
		file.seek(0)                            # 回到第一行，因为每次使用readline，文本都会后退一行
		for i in range(raw_number):	        	# 计算文件的总行数，设计循环逐行读入数据	
			text_line = file.readline()     	# 每次调用该函数使文件下移一行
			# temp_file = text_line.strip().split(' ', 32)              # temp_file存储分割后的数据列表
			# attribute_data = temp_file[0:2] + temp_file[12:32]        # 存储要使用的数据列
			temp_file = text_line.strip().split(' ', 4)             	# temp_file存储分割后的数据列表
			attribute_data = temp_file[0:4]      # 存储要使用的数据列

			for data in attribute_data:          # 逐行写入数据,只能每个字符分别读入，才可以在数据中间插入空格
				file2.writelines(data)
				file2.writelines(' ')            # 数据间加入空格
			file2.writelines('\n')               # 数据换行

except IOError as err:
    print 'File error:' + str(err)
print attribute_data 
````
###  3.python函数shutil，实现文件的复制
使用`shutil.copy(str_file1,str_file2)`将文件str_file1，复制到文件str_file2的位置

### 4.python实现读取文件夹里文件名功能
```python
import os 
path = 'the path of the directory'    # 读取文件路径
list_name = os.listdir(path)            # 读取路径下文件和文件夹的名
```
则list_name就是文件夹里所以子文件夹和文件的列表，可以通过os.path.isfile和os.path.isdir来对列表里的项进行判断看是文件还是文件夹。