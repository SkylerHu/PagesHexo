---
title: Mac修正Vim无法git提交的问题
date: 2016-11-28 15:31:05
tags:
  - git
---
Git 可以使用Vim作为 Commit Message编辑器，上次不小心出现了以下错误：
```shell
error: There was a problem with the editor 'vim'.Please supply the message using either -m or -F option.
```
如果在 Vim 中编辑文本时因为按键失误出现类似这样：*E492: Not an editor command…* 的错误信息时，必然无法提交。

后来在 [Google Group: vim_mac](http://groups.google.com/group/vim_mac/browse_thread/thread/0d33e2f2130867b0) 这个帖子中找到了解决的办法，就是使用完整的 Vim 路径—— /usr/bin/vim :
```shell
git config --global core.editor /usr/bin/vim
```

看了一些别的博客，说有可能的原因是：
`Vim 在遇到 Exx Error 时返回 Non-Zero code 是为了兼容 Posix，不过这种情况应该只会出现在使用 Ex Mode 时，Normal/Insert Mode 是不会这样的。`
