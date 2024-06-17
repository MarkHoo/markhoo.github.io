---
title: Go关键字和预定义标识符
date: 2018-10-14 12:01:34
cover: 
categories: Go
tags:
- Go
---

> Go关键字和预定义标识符

<!--more-->


#### Go 语言中的关键字(25个)

关键字|描述
-|-
break | 退出循环
default | 选择结构默认项（switch、select）
func | 定义函数
interface | 定义接口
select | channel
case | 选择结构标签
chan | 定义channel
const | 常量
continue | 跳过本次循环
defer | 延迟执行内容（收尾工作）
go | 并发执行
map | map类型
struct | 定义结构体
else | 选择结构
goto | 跳转语句
package | 包
switch | 选择结构
fallthrough | 流程控制
if | 选择结构
range | 从slice、map等结构中取元素
type | 定义类型
for | 循环
import | 导入包
return | 返回
var | 定义变量


#### 预定义标识符(36个)

-|-|-|-|-|-
-|-|-|-|-|-
append|bool|byte|cap|close|complex
complex64|complex128|uint16|copy|false|float32
float64|imag|int|int8|int16|uint32
int32|int64|iota|len|make|new	
nil|panic|uint64|print|println|real
recover|string|true|uint|uint8|uintptr

Go语言的语言符号又称为记法元素，共包括5类，标签符(identifier)、关键字(keyword)、操作符(operator)、分隔符(delimiter)、字面量(literal),它们是组成Go语言代码和程序的最基本单位。

Go语言的所有源代码都必须由Unicode编码规范的UTF-8编码格式进行编码。

