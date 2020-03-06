---
title:  Go语言
categories: [杂记]
tags:   [Go, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-03-3 04:51:55
---

go语言知识点

## go语言结构
Go 语言的基础组成有以下几个部分：

- 包声明
- 引入包
- 函数
- 变量
- 语句 & 表达式
- 注释

接下来让我们来看下简单的代码，该代码输出了"Hello World!":

实例
```go
package main

import "fmt"

func main() {
   /* 这是我的第一个简单的程序 */
   fmt.Println("Hello, World!")
}
```

运行
```bash
$ go build hello.go
//生成二进制文件
$ go run hello.go
//直接运行
```

## go基本语法

### 标记
可以是关键字，标识符，常量，字符串，符号。

### 行分隔符
  - 一行代表一个语句结束
  - 多个语句写在同一行，它们则必须使用 ; 人为区分

### 注释
// 或者 /* 和 */

### 标识符
用来命名变量、类型等程序实体。一个标识符实际上就是一个或是多个字母(A~Z和a~z)数字(0~9)、下划线_组成的序列，但是第一个字符必须是字母或下划线而不能是数字

### 字符串连接
Go 语言的字符串可以通过 + 实现：
```go
fmt.Println("Google" + "Runoob")
```
### 关键字
 25 个关键字或保留字
 
    break	    default	    func	interface	select
    case	    defer	    go	    map 	    struct
    chan	    else	    goto	package	    switch
    const	    fallthrough	if	    range	    type
    continue	for	        import	return	    var


除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符：

    append	bool	byte	cap	        close	complex	complex64	complex128	uint16
    copy	false	float32	float64	    imag	int	    int8	    int16	    uint32
    int32	int64	iota	len	        make	new	    nil	        panic	    uint64
    print	println	real	recover	    string	true	uint	    uint8	    uintptr

### 空格
Go 语言中变量的声明必须使用空格隔开

## go语言数据类型

Go 语言按类别有以下几种数据类型：

|类型|描述|  
|:-----------|:--------------------|
|布尔型|布尔型的值只可以是常量 true 或者 false。一个简单的例子：var b bool = true。
|数字类型|整型 int 和浮点型 float32、float64，Go 语言支持整型和浮点型数字，并且支持复数，其中位的运算采用补码。
|字符串类型|字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本。
|派生类型| 指针类型  数组类型  结构化类型(struct)   Channel 类型  函数类型  切片类型  接口类型（interface） Map 类型

|数字类型|描述|   
|:-------|:-------|
|uint8|无符号 8 位整型 (0 到 255)
|uint16|无符号 16 位整型 (0 到 65535)
|uint32|无符号 32 位整型 (0 到 4294967295)
|uint64|无符号 64 位整型 (0 到 18446744073709551615)
|int8|有符号 8 位整型 (-128 到 127)
|int16|有符号 16 位整型 (-32768 到 32767)
|int32|有符号 32 位整型 (-2147483648 到 2147483647)
|int64|有符号 64 位整型 (-9223372036854775808 到 9223372036854775807)

|浮点类型|描述|   
|:--------|:---------|
|float32|IEEE-754 32位浮点型数
|float64|IEEE-754 64位浮点型数
|complex64|32 位实数和虚数
|complex128|64 位实数和虚数

|其他类型|描述|   
|:------------|:---------|
|byte|类似 uint8
|rune|类似 int32
|uint|32 或 64 位
|int|与 uint 一样大小
|uintptr|无符号整型，用于存放一个指针

## go语言变量

### 变量声明
- 第一种，使用 var 指定变量类型，如果没有初始化，则变量默认为零值。
    ```go
    var v_name v_type
    v_name = value
    ```

    零值就是变量没有做初始化时系统默认设置的值。


        数值类型（包括complex64/128）为 0

        布尔类型为 false

        字符串为 ""（空字符串）

        以下几种类型为 nil：

            var a *int
            var a []int
            var a map[string] int
            var a chan int
            var a func(string) int
            var a error // error 是接口

- 第二种，根据值自行判定变量类型。
    ```go
    var v_name = value
    ```
- 第三种，省略 var, 使用 :=   
   左侧如果没有声明新的变量，就产生编译错误，格式：
    ```go
    v_name := value
    ```
- 多变量声明
    ```go
    //类型相同多个变量, 非全局变量
    var vname1, vname2, vname3 type
    vname1, vname2, vname3 = v1, v2, v3

    var vname1, vname2, vname3 = v1, v2, v3 // 和 python 很像,不需要显示声明类型，自动推断

    vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经被声明过的，否则会导致编译错误


    // 这种因式分解关键字的写法一般用于声明全局变量
    var (
        vname1 v_type1
        vname2 v_type2
    )
    ```

    ### 值类型和引用类型
    ---
    go语言只有值传递

    ---

     所有像 int、float、bool 和 string 这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值：

    - 当使用等号 = 将一个变量的值赋值给另一个变量时，如：j = i，实际上是在内存中将 i 的值进行了拷贝：

    -  &i 来获取变量 i 的内存地址，例如：0xf840000040（每次的地址都可能不一样）。值类型的变量的值存储在栈中。内存地址会根据机器的不同而有所不同，甚至相同的程序在不同的机器上执行后也会有不同的内存地址。因为每台机器可能有不同的存储器布局，并且位置分配也可能不同。

    更复杂的数据通常会需要使用多个字，这些数据一般使用引用类型保存。

    - 一个引用类型的变量 r1 存储的是 r1 的值所在的内存地址（数字），或内存地址中第一个字所在的位置。


    - 这个内存地址为称之为指针，这个指针实际上也被存在另外的某一个字中。

    - 同一个引用类型的指针指向的多个字可以是在连续的内存地址中（内存布局是连续的），这也是计算效率最高的一种存储形式；也可以将这些字分散存放在内存中，每个字都指示了下一个字所在的内存地址。

    当使用赋值语句 r2 = r1 时，只有引用（地址）被复制。

    如果 r1 的值被改变了，那么这个值的所有引用都会指向被修改后的内容，在这个例子中，r2 也会受到影响。

## Go语言常量
常量是一个简单值的标识符，在程序运行时，不会被修改的量。

常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

常量的定义格式：
```go
const b string = "abc" //显式类型定义   
const b = "abc" //隐式类型定义
//可以省略类型说明符 [type]
```



