# Vim入门及心得分享

[TOC]

## 什么是Vim
> Vim is a **highly configurable** text editor built to enable **efficient text editing**. It is an improved version of the vi editor distributed with most UNIX systems.
> ——Vim官网

## 学习Vim有什么优势
1. 所有Unix/Linux操作系统默认配备的编辑器，被许多命令默认调用（man、less等）
2. 加强对正则的认识
3. 全键盘操作，熟悉键盘后大幅提高编辑效率
4. 快捷键被许多IDE和编辑器所支持，迁移门槛低

## 操作基础
![shortcut](https://www.evernote.com/shard/s613/res/df6c3588-e04f-48fe-b495-182b812d24bf)
核心原则：**操作[ + 数量][ + 对象][ + 位置]**

三种模式：
1. Normal 大多数情况下处于的模式
2. Insert 键盘的输入原样反映在文本上
3. Visual 对文本块做指定的操作
4. Command 设置vim状态或执行其他命令

### 移动 
`[n]h/j/k/l`	光标向左、下、上、右移动
`Ctrl + f/b`	以页为单位向下、向上移动
`gg/[n]G`	文件开头、结尾
`0/^/$`	本行开头、字符开头、结尾
`[n]w/b`	以单词为单位向右、向左移动

### 查找
`*/#`	同名符号向下、向上查找
`n/N`	查找下一个、上一个

### 编辑
`[n]yy/dd`	行的复制、剪切
`p/P`	向下、向上粘贴
`[n]>>/<<`	增大、减小缩进
`u`	撤销上一操作
`Ctrl + r`	重新执行撤销的操作

## 场景展示
1. 下一行合并到当前行`J`
2. 回到上一次编辑的位置``.`
3. 字母大小写转换`~`
4. 单词大小写转换`guw/gUw`
5. 替换当前字符`r`
6. 剪切括号（成对对象）内的内容`di(`
7. 录制宏`qq`，并应用`[n]@q`
8. 记录当前位置`mm`，之后返回记录的位置``m`
9. 使用正则查找尾部空格

## 配置文件及插件安装
1. ~/.vimrc
2. 安装插件管理工具
`git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim`
3. 在命令模式下使用`PluginInstall`，`PluginClean`管理插件

## vim的不足
1. 调试
2. 阅读项目代码