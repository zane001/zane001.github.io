title: "Swift学习笔记一"
date: 2015-10-17
tags: "swift"
categories: "编程"
---
1. CocoaPods下载第三方库较慢的解决方法，不更新本地库
>解决方法：pod update –verbose –no-repo-update
2. OS X EI Capitan 10.11安装CocoaPods的命令
>解决方法：sudo gem install -n /usr/local/bin cocoapods

3. 出现 [!] Unable to satisfy the following requirements: - Kingfisher (~> 1.6) required by `Podfile
>解决方法：更新本地库 pod update –verbose
4. 出现 Illegal Configuration: Container Views cannot be placed in elements that are repeated at runtime.
>解决方法：把Container View换成View
5. EXC_BAD_ACCESS错误
>解决方法：可以这么说，90%的错误来源在于对一个已经释放的对象进行release操作。
6. Xcode快捷键
 1. 移动代码
>注意：在移动代码时都是移动单行时无需选中，鼠标点击那一行的任何地方都可以移动，多行时需选中。
代码左移: command + [
代码右移: command + ]
 2. 代码上移：option + command +[
 3. 代码下移：option + command +]
 4. 将方法或者注释收起、展开
>使用时只要鼠标在方法或注释的范围内就好。
收起：option + command + 左箭头
展开：option + command + 右箭头
7. 在未保存代码之前，Command + Q关闭Xcode，导致代码丢失，以后关闭Xcode时记得Command + S保存。
