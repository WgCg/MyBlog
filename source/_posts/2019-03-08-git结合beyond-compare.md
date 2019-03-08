---
title: git结合beyond-compare
tags: [git, beyond compare]
toc: true
mathjax: true
date: 2019-03-08 15:10:44
categories:
- 其它技术
- git
---

# beyond compare 无限试用

## for mac

1. 应用程序 -> beyond cmpare右击显示包内容 -> contents -> macos
2. 在当前目录下新建文件BCompare.sh，把原来的BCompare重命名为BCompare.real
3. 编辑BCompare.sh，输入以下代码:

    ```````````````````````bash
    #!/bin/bash
    rm "/Users/$(whoami)/Library/Application Support/Beyond Compare/registry.dat"
    "`dirname "$0"`"/BCompare.real $@

    ```````````````````````

4. 右击BCompare.sh -> 显示简介 -> 删除文件名后缀.sh

# .gitconfig配置

.gitconfig位于user下, shift + comand + . 显示隐藏文件

``````````````txt
[alias]
    dift = "difftool --dir-diff --no-symlinks"
[diff]
    tool = bcomp
[difftool]
    prompt = false
[difftool "bcomp"]
    trustExitCode = true
    cmd = "/usr/local/bin/bcomp" \"$LOCAL\" \"$REMOTE\"
[merge]
    tool = bcomp
[mergetool]
    prompt = false
[mergetool "bcomp"]
    trustExitCode = true
    cmd = "/usr/local/bin/bcomp" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\"
``````````````

1. git dift origin/dev：当前分支与远程dev分支进行比较