---
layout:     post
title:      VIM常用操作
subtitle: 笔记
date: 2020-11-23
author:     Jingming
status: public
catalog: true
tags:
    - Shell
---

VIM三种模式：命令模式、输入模式、底线命令模式。

### 一、命令模式

#### 1. 移动光标
0 到行头

$ 到行尾

G 到文件最后一行

gg 到文件第一行

H 到当前屏幕第一行

M 到当前屏幕中间一行

L 到当前屏幕最后一行

/单词  到匹配的单词处，之后按 n 到下一个匹配的单词处

#### 2. 复制、删除、粘贴

shift v + 移动键 选中文本

y 复制； 可以之后输入数字，表示复制多少次

d 剪切

p 粘贴

dd 删除一整行

ndd 删除光标所在向下n行

x 删除当前光标字符

#### 3. 撤销、还原

u 取消上次操作

ctrl r 重做上一个操作

#### 4. 进入其他模式

i 进入输入模式
：进入底线命令模式

### 二、输入模式
按ESC 退出输入模式，进入命令模式。

i 进入当前光标处进行输入

I 进入当前光标后面的第一个非空格处开始输入

a 到当前行的开头进行输入（在命令模式下输入a）

A 到当前行的最后进行输入

o 到当前行的下一行开头进行输入

O 到当前行的上一行开头进行输入

#### 多行修改

Ctrl-V + 方向键  I

### 三、底线命令模式
按回车执行命令，每次结束后返回命令模式。

:q 退出文件

:w 保存文件

:wq 保存并退出文件

:q! 强退

:w 文件名 另存为

:set nu 显示行号

:set nonu 取消行号显示

### 四、～/.vimrc配置

set nu

syntax on 语法高亮

参考：https://github.com/skywind3000/awesome-cheatsheets/blob/master/editors/vim.txt