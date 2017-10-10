---
title: Android Studio查看项目所有依赖的lib
date: 2016-12-29 19:26:38
tags:
  - Android
---
添加task
```
dependencies {
    ...
}
//显示所有依赖包的存储路径以及版本
task showLibInfo << {
    configurations.compile.each { println it }
}
```

然后在项目目录下执行task
```bash
./gradlew showLibInfo
```

输出结果就可以看到项目所有依赖的lib信息了
