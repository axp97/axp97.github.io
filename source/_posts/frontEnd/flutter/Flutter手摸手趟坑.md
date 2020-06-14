---
title: Flutter手摸手趟坑
date: 2020-06-14 12:30:00
author: 安小盼
categories: 前端
tags:
  - Flutter
---

前段时间由于公司需要，赶鸭子上架，用 Flutter 做了个简单 app，记录下实际中遇到的小坑~

#### 1. 无法将"Unblock-File”项识别为 cndlet、 函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确，然后重试。

<span style="color: green;">解决：到 windows 官网更新最新版的 powershell 5.0 版本，安装后重启并再次运行 `flutter doctor` </span>

1.1、找到 PowerShell 程序，进入命令行窗口；
win+R 输入`PowerShell`；在命令行窗口中输入命令：`$PSVersionTable.PSVersion`
![4815c08233054a3a9e10834042866e24.png](en-resource://database/4694:1)
（实际版本会低于 5.0 才会报错）

1.2、点击下载 [Windows Management Framework 5.1](https://www.microsoft.com/en-us/download/details.aspx?id=54616)
![5b04a066975eec1656144803c8e21050.png](en-resource://database/4695:1)
**安装程序遇到错误：0x80240037**

2、把 Win7AndW2K8R2-KB3191566-x64.msu 的后缀名 `.msu` 改成 `.cab` 并解压到新文件夹中 cab（名字任起，我把它移到了 E 盘了）
![12f99cb4a25c46d589616bbf69ac0e88.png](en-resource://database/4696:1)

3、打开 cmd，以管理员身份运行)，输入：

`dism /online /add-package /packagepath:"文件名.cab的完整路径"`

注：解压出来可能会有多个.cab，注意输入对应好的文件名.cab（前面下载的名字中是：KB2819745）安装好后，会提示是否立即重新启动：y（重新启动）

![c937cad7c98c9e04edca6e1b07c9bac0.png](en-resource://database/4697:1)

4、cmd 输入 / 双击 flutter_console.bat 后输入 /打开 PowerShell 输入： `flutter doctor`

`WSUSSCAN.cab 是什么？cab 是什么文件？*.cab是微软制定的压缩包文件，常用于软件的安装程序。WSUSSCAN.cab 应该是更新补丁检测文件 补丁的扫描、下载和安装 在安装服务器的机器上同时运行着补丁下载器， 它会自动下载并更新补丁检测文件(wsusscan.cab)`

#### 2. flutter sdk 降级、回退、升级降级到指定的版本方法

<span style="color: green;">解决：`git reset --hard <commit版本号>`</span>

- 进入[Flutter Git 源码](https://github.com/flutter/flutter)，release > tags > 拿到 commit 版本

**切换 flutter sdk**
flutter 安装目录中执行：`git checkout v0.1.9`\*\*\*\*
然后运行：`flutter doctor`

#### 3. Waiting for another flutter command to release the startup lock...

<span style="color: green;">解决：删除 lockfile 文件</span>

- 打开 flutter/bin/cache/lockfile，删除
- 或者用命令：`rm ./flutter/bin/cache/lockfile`

#### 4. Flutter android GradleException 显示红色报错解决办法

<span style="color: green;">解决：</span>

```
throw new GradleException(...)
```

替换

```
throw new Exception(...)
```
