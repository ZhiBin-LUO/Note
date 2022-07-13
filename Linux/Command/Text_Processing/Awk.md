[TOC]

## awk

> **awk** 是一种编程语言，用于在linux/unix下对文本和数据进行处理。支持用户自定义函数和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。awk有很多内建的功能，比如数组、函数等，这是它和C语言的相同之处，灵活性是awk最大的优势。



#### 命令语法

```shell
awk [options] 'script' file
awk [options] -f scriptfile file

# 脚本(script) = 模式(pattern) + 操作(action)
```

##### 常用命令选项

- `-F fs`  fs指定输入分隔符，fs可以是字符串或正则表达式。默认是空格/制表符
- `-f scripfile`  从脚本文件中读取awk命令
- `-v var=value`  赋值一个用户定义变量，将外部变量传递给awk



#### awk 脚本

##### 模式

- /正则表达式/：使用通配符的扩展集
- 关系表达式：使用运算符进行操作，可以是字符串或数字的比较测试
- 模式匹配表达式：用运算符`~`（匹配）和`!~`（不匹配）
- BEGIN语句块/pattern语句块/END语句块

#####  操作

> 由一个或多个命令、函数、表达式组成，之间由换行符或分号隔开，并位于大括号内

- 变量或数组赋值
- 输出命令
- 内置函数
- 控制流语句

##### 工作原理

```shell
awk 'BEGIN{ commands } pattern{ commands } END{ commands }'

- 第一步：执行BEGIN{ commands }语句块中的语句
- 第二步：从文件或标准输入读取一行，然后执行pattern{ commands }语句块，它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕。
- 第三步：当读至输入流末尾时，执行END{ commands }语句块
```















#### 参考链接

[命令参考](https://wangchujiang.com/linux-command/c/awk.html)
[官方手册](https://www.gnu.org/software/gawk/manual/gawk.html)

