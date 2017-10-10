---
title: Linux命令awk
date: 2017-01-10 20:36:33
tags:
  - Linux
  - Shell
---
### 一、简介

awk是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入、一个或多个文件，或其它命令的输出。它支持用户自定义函数和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。

awk的处理文本和数据的方式：它逐行扫描文件，从第一行到最后一行，寻找匹配的特定模式的行，并在这些行上进行你想要的操作。如果没有指定处理动作，则把匹配的行显示到标准输出(屏幕)，如果没有指定模式，则所有被操作所指定的行都被处理。

awk分别代表其作者姓氏的第一个字母。因为它的作者是三个人，分别是Alfred Aho、Brian Kernighan、Peter Weinberger。

gawk是awk的GNU版本，它提供了Bell实验室和GNU的一些扩展。下面介绍的awk是以GUN的gawk为例的，在linux系统中已把awk链接到gawk，所以下面全部以awk进行介绍。

### 二、使用

`awk [-F  field-separator]  'commands'  input-file(s)`
其中，[-F域分隔符]是可选的，commands 是真正awk命令。 input-file(s) 是待处理的文件。
在awk中，文件的每一行中，由域分隔符分开的每一项称为一个域。通常，在不指名-F域分隔符的情况下，默认的域分隔符是空格。

### 三、模式

1. 正则表达式：使用通配符的扩展集。
2. 关系表达式：可以用下面运算符表中的关系运算符进行操作
3. 可以是字符串或数字的比较，如$2>%1选择第二个字段比第一个字段长的行。
4. 模式匹配表达式：用运算符~(匹配)和~!(不匹配)。
5. 模式，模式：指定一个行的范围。该语法不能包括BEGIN和END模式。
6. BEGIN：让用户指定在第一条输入记录被处理之前所发生的动作，通常可在这里设置全局变量。
7. END：让用户在最后一条输入记录被读取之后发生的动作。

### 四、常量

|常量名|含义|
| -- | -- |
| $n | 当前记录的第n个字段，字段间由FS分隔 |
| $0 | 完整的输入记录 |
| NF | 浏览记录的域的个数 |
| NR | 已读的记录数 |
| ARGC | 命令行参数个数 |
| ARGV | 命令行参数排列 |
| ENVIRON | 支持队列中系统环境变量的使用 |
| FILENAME | awk浏览的文件名 |
| FNR | 浏览文件的记录数 |
| FS | 设置输入域分隔符，等价于命令行 -F选项 |
| OFS | 输出域分隔符 |
| ORS | 输出记录分隔符 |
| RS | 控制记录分隔符 |

### 五、常用函数

###### 1. 求和
```shell
cat data | awk '{sum+=$1} END {print "Sum = ", sum}'
```
###### 2. 求平均
```shell
cat data | awk '{sum+=$1} END {print "Average = ", sum/NR}'
```
###### 3. 求最大值
```shell
cat data | awk 'BEGIN {max = 0} {if ($1>max) max=$1 fi} END {print "Max=", max}'
```
###### 4. 求最小值（min的初始值设置一个超大数即可）
```shell
cat data | awk 'BEGIN {min = 1999999} {if ($1<min) min=$1 fi} END {print "Min=", min}'
```
###### 5. 去重
```shell
awk -F':' '!a[$1]++'
```
###### 6. 输入统计结果
```shell
cat data | awk 'info[$0]=++a[$12] {} END {print "TotalCount = ", NR; for(i in info){print i, info[i]} }'
```

还可以结合 `grep`/`uniq`(去重)/`sort`(排序)等命令一起使用

##### 六、Demo

当前目录下有三个log文件如下
log1.txt
``` 
2016-11-29 17:10:41,967 GET /api/web/conversation skyler
2016-11-29 17:10:41,967 GET /api/web/conversation hu
2016-11-29 17:10:41,967 GET /api/web/conversation hu
2016-11-29 17:12:12,434 GET /api/web/tag -
```
log2.txt
```
2016-11-29 17:08:19,794 GET /api/web/account/user huleping
2016-11-29 17:10:41,967 GET /api/web/conversation skyler
2016-11-29 17:08:19,794 GET /api/web/account/user skyler
```
log3.txt
```
2016-11-29 17:12:23,462 GET /api/web/conversation skyler
2016-11-29 17:12:30,459 GET /api/web/activity huleping
2016-11-29 17:12:30,474 GET /api/web/conversation huleping
```

查找出匹配结果相同的结果，然后根据用户id去重统计个数，并输出总数
```shell
➜ for i in $(seq 1 3); do grep "/api/web/conversation" log${i}.txt; done | awk '!a[$5]++ END {print "TotalCount = ", NR; for(i in a){print i, a[i] | "sort -r -n -k2";} }'
2016-11-29 17:10:41,967 GET /api/web/conversation skyler
2016-11-29 17:10:41,967 GET /api/web/conversation hu
2016-11-29 17:12:30,474 GET /api/web/conversation huleping
TotalCount =  6
skyler 3
hu 2
huleping 1
```
----
*`seq` 用于产生从某个数到另外一个数之间的所有整数*
*[sort用法说明](http://man.linuxde.net/sort)*
*n是按照数字大小排序，-r是以相反顺序，-k是指定需要爱排序的栏位，-t指定栏位分隔符为冒号*
