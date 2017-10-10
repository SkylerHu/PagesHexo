---
title: 清除项目所有pyc文件
date: 2016-12-26 14:39:00
tags:
    - Python
---
>清除python项目所有pyc文件
用python去写一个项目，难免有时候需要处理清除pyc文件，于是写了个脚本，

可以传入一个文件夹参数，表示需要清理的文件夹
默认取当前执行的文件夹路径

脚本具体如下：
```bash
#!/usr/bin/env python
# coding=utf-8
# Date:2011-03-26

import os
import fnmatch
import sys


def clearpyc(root, patterns='*', single_level=False, yield_folders=False):
    """
    root: 需要遍历的目录
    patterns： 需要查找的文件，以；为分割的字符串
    single_level: 是否只遍历单层目录，默认为否
    yield_folders: 是否包含目录本身，默认为否
    """
    patterns = patterns.split(';')
    for path, subdirs, files in os.walk(root):
        if yield_folders:
            files.extend(subdirs)
            files.sort()
        for name in files:
            for pattern in patterns:
                if fnmatch.fnmatch(name, pattern.strip()):  # 去除pattern两端的空格
                    yield os.path.join(path, name)
        if single_level:
            break


if __name__ == '__main__':
    if 2 == len(sys.argv):
        directory = os.path.join(os.getcwd(), sys.argv[1])
        print("参数检查正确")
    else:
        directory = os.getcwd()
        print("取当前目录")

    print(directory)

    for path in clearpyc(directory, '*.pyc'):
        print(path)
        os.remove(path)

```
