---
title: Python学习笔记
date: 2017-10-11 11:34:25
tags: Python
---

#### 根据Soure安装指定版本Python

遇到问题，安装完之后没有Pip

需要执行
```
./configure --with-ensurepip=upgrade
make && make install
```

其中`--with-ensurepip=upgrade` 很重要


#### Python3 print中文

```Python
In [1]: arr  = ['我是中文', '好的']

In [2]: print(arr)
['我是中文', '好的']

In [3]: import json

In [4]: astr = json.dumps(arr)

In [5]: print(astr)
["\u6211\u662f\u4e2d\u6587", "\u597d\u7684"]

In [6]: print(astr.encode('utf-8').decode('unicode-escape'))
["我是中文", "好的"]

In [7]: astr
Out[7]: '["\\u6211\\u662f\\u4e2d\\u6587", "\\u597d\\u7684"]'

```


#### git pre-commit.md

需要提前 `pip install flake8`
```
#!/usr/bin/env bash

if [ -n "$BYPASS" ]; then exit 0; fi

git diff --cached -- '*.py' | flake8 --max-line-length=119 --ignore=W391,E501,F401 --show-source --diff

if [ $? -gt 0 ]; then
    echo
    echo '--------'
    echo 'Lint check failed.'
    exit 1
fi
```
