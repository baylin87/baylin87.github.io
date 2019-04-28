---
title: RN Keyboard dismiss
tags:
  - JavaScript
comments: true
date: 2019-04-27 12:10:45
categories: 编程开发
---

> 公司用的RN锁定0.30，很多新功能或者老bug只能手动修复，蛋疼。

<!--more-->

# 现象

当点击TextInput键盘弹起时，如果此时后端报错，input不失焦，键盘也不隐藏，emmmmm

# 解法

## TextInput

```javascript
blurOnSubmit
```

or

```javascript
  TextInput.State.blurTextInput(TextInput.State.currentlyFocusedField())
```

## Keyboard

手动调用 `keyboard.dismiss()`

## other
