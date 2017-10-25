---
title: Django添加sql query log
date: 2017-10-11 11:32:31
tags: Django
---

### 在manage.py shell中

```
import logging
l = logging.getLogger('django.db.backends')
l.setLevel(logging.DEBUG)
l.addHandler(logging.StreamHandler())
```

### 在console中
在`settings.py`中配置

```
LOGGING = {
    ...

    'loggers': {
        'django.db.backends': {
            'handlers': ['console'],  # Quiet by default!
            'propagate': False,
            'level': 'DEBUG',
         },
    }
}

```
