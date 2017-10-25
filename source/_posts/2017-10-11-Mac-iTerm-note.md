---
title: Mac使用Iterm2的正确姿势
date: 2017-10-11 11:37:23
tags: Mac
---

#### iTerm2设置光标跳动

`Preferences > Profiles > Keys > `

修改配置项

`⌥←` --> `Send Escape Sequence` + `Esc+b`

`⌥→` --> `Send Escape Sequence` + `Esc+f`


#### 自定义zsh提示命令
前提当然是试用zsh主题了
示例：
```
gmserver() {
    ssh root@$1.$2.gengmei
}
_gmserver() {
    _arguments '-s[sort output]' '1:first arg:(backend gaia ascle ascle-mock backend-mock flagship artemis)' ':next arg:(dev dev1 test pre hotfix hotfix1)'
}
compdef _gmserver gmserver
```
