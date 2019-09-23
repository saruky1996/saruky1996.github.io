---
title: PHP基础学习笔记
date: 2016-07-17 22:12:35
categories: 
  - 学习
tags: 
  - PHP
  - 学习记录
  - 老坑迁移
---

暑假里帮南林记忆做了软件著作权的申请，顺便就开始了php的学习之路。
HTML5基础一点点，从w3cschool开始自学，顺便用博客防止自己摸鱼或者半路弃坑。

<!-- more -->

### 关于安装
__PHP 服务器组件__
<font size = "2">选用了[WampServer](http://www.wampserver.com/en/ "WampServer-En")
需要注意的是，在安装Apache的时候计算机中要求有VC库。我当时机子里就没有，在网上找到了正确的解决方案，顺便提供一下备份[网盘](https://pan.baidu.com/s/1jInnNRC "VC-redist")</font>
__PHP IDE__
<font size = "2">Eclipse</font>
__MySql管理工具__
<font size = "2">Navicat，然而还没装。</font>
__文本编辑器__
<font size = "2">直接用Sublime Text。</font>

<br>
### 关于自学
自学网站[w3cschool](http://www.runoob.com/php/php-tutorial.html)
网上好像有其他奇怪的相似域名，感觉这款应该是正宗的，它改域名了现在叫菜鸟教程。

<br>
### 第一部分 语法基础
#### 基本框架
```bash
<? php
	// code of php
?>
```
<font size = "2">PHP可以和HTML标签混合使用</font>
```bash
<!DOCTYPE html>
<html>
<body>
<? php
	// code of php
?>
</body>
</html>
```