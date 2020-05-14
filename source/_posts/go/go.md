---
title:  Go_基础
categories: [杂记]
tags:   [Go, Note]
copyright: false``
reward: false
rating: false
related_posts: false
date: 2020-03-3 04:51:55
---

go语言知识点   
看到一个比较好的在线教程网站[地址](https://tour.go-zh.org/basics)

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

||||||    
|:------|:------|:-----|:-------|:-----|        
|break|default|func|interface|select|   
|case|defer|go|map|struct|
|chan|else|goto|package|switch
|const|fallthrough|if|range|type
|continue|for|import|return|var


除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符：

|||||||||
|:------|:------|:------|:------|:------|:------|:------|:------|   
|append|bool|byte|cap|close|complex|complex64|complex128|uint16
|copy|false|float32|float64|imag|int|int8|int16|uint32
|int32|int64|iota|len|make|new|nil|panic|uint64
|print|println|real|recover|string|true|uint|uint8|uintptr

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
### const
常量是一个简单值的标识符，在程序运行时，不会被修改的量。

常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

常量的定义格式：
```go
const b string = "abc" //显式类型定义   
const b = "abc" //隐式类型定义
//可以省略类型说明符 [type]
```
常量可以用len(), cap(), unsafe.Sizeof()函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过。
iota
iota，特殊常量，可以认为是一个可以被编译器修改的常量。

### iota 
在 const关键字出现时将被重置为 0(const 内部的第一行之前)，const 中每新增一行常量声明将使 iota 计数一次(iota 可理解为 const 语句块中的行索引)。

iota 可以被用作枚举值：
```go
const (
    a = iota
    b = iota
    c = iota
)
```
第一个 iota 等于 0，每当 iota 在新的一行被使用时，它的值都会自动加 1；所以 a=0, b=1, c=2 可以简写为如下形式：
```go
package main

import "fmt"

func main() {
    const (
            a = iota   //0
            b          //1
            c          //2
            d = "ha"   //独立值，iota += 1
            e          //"ha"   iota += 1
            f = 100    //iota +=1
            g          //100  iota +=1
            h = iota   //7,恢复计数
            i          //8
    )
    fmt.Println(a,b,c,d,e,f,g,h,i)
}
```
以上运行结果为：

    0 1 2 ha ha 100 100 7 8

再看个有趣的的 iota 实例：
```go
package main

import "fmt"
const (
    i=1<<iota
    j=3<<iota
    k
    l
)

func main() {
    fmt.Println("i=",i)
    fmt.Println("j=",j)
    fmt.Println("k=",k)
    fmt.Println("l=",l)
}
```
以上运行结果为：

    i= 1
    j= 6
    k= 12
    l= 24
iota 表示从 0 开始自动加 1，所以 i=1<<0, j=3<<1（<< 表示左移的意思），即：i=1, j=6，这没问题，关键在 k 和 l，从输出结果看 k=3<<2，l=3<<3。

简单表述:

    i=1：左移 0 位,不变仍为 1;
    j=3：左移 1 位,变为二进制 110, 即 6;
    k=3：左移 2 位,变为二进制 1100, 即 12;
    l=3：左移 3 位,变为二进制 11000,即 24。

## Go语言运算符

|||||||||      
|:------|:------|:-----|:-------|:-----|:-----|:-----|:------|     
|算数运算符|+|-|*|/|%|++|--|  
|关系运算符|==|!=|>|<|>|>=|<=   
|逻辑运算符|&&|\|\||!|    
|为运算符|&|\||^(异或)|<<(左移)|>>(右移)
|其他运算符|&(取地址)|*(指针)


|优先级|运算符|      
|:------|:------|
|5|	* / % << >> & &^
|4|	+ - \| ^
|3|	== != < <= > >=
|2|	&&
|1|	\|\||


## go语言条件语句
### if
### switch

- switch 语句用于基于不同条件执行不同动作，每一个 case 分支都是唯一的，从上至下逐一测试，直到匹配为止。

 - switch 语句执行的过程从上至下，直到找到匹配项，匹配项后面也不需要再加 break。

 - switch 默认情况下 case 最后自带 break 语句，匹配成功后就不会执行其他 case，如果我们需要执行后面的 case，可以使用 fallthrough 。

```go
package main

import "fmt"

func main() {
   /* 定义局部变量 */
   var grade string = "B"
   var marks int = 90

   switch marks {
      case 90: grade = "A"
      case 80: grade = "B"
      case 50,60,70 : grade = "C"
      default: grade = "D"  
   }

   switch {
      case grade == "A" :
         fmt.Printf("优秀!\n" )    
      case grade == "B", grade == "C" :
         fmt.Printf("良好\n" )      
      case grade == "D" :
         fmt.Printf("及格\n" )      
      case grade == "F":
         fmt.Printf("不及格\n" )
      default:
         fmt.Printf("差\n" );
   }
   fmt.Printf("你的等级是 %s\n", grade );      
}
```

## go语言循环语句

集中for循环写法:

- 
        for i := 0; i <= 10; i++ {
                sum += i
        }

- 
         for (i := 0; i <= 10; i++) {
                sum += i
        }
-         
        for sum <= 10{
                sum += sum
        }
        // 类似 while
       
- for 循环的 range 格式可以对 slice、map、数组、字符串等进行迭代循环。格式如下：

        for key, value := range oldMap {
            newMap[key] = value
        }

循环关键字:


|关键字|描述|      
|:------|:------|
| break|经常用于中断当前 for 循环或跳出 switch 语句
| continue|跳过当前循环的剩余语句，然后继续进行下一轮循环。
| goto|将控制转移到被标记的语句。

tips:  
goto 语句可以无条件地转移到过程中指定的行。   
goto 语句通常与条件语句配合使用。可用来实现条件转移， 构成循环，跳出循环体等功能。

```go
package main

import "fmt"

func main() {
   /* 定义局部变量 */
   var a int = 10

   /* 循环 */
   LOOP: for a < 20 {
      if a == 15 {
         /* 跳过迭代 */
         a = a + 1
         goto LOOP
      }
      fmt.Printf("a的值为 : %d\n", a)
      a++    
   }  
}
```
输出:   

    a的值为 : 10
    a的值为 : 11
    a的值为 : 12
    a的值为 : 13
    a的值为 : 14
    a的值为 : 16
    a的值为 : 17
    a的值为 : 18
    a的值为 : 19


## go语言函数

Go 语言函数定义格式如下：

    func function_name( [parameter list] ) [return_types] {
    函数体
    }

---
Go 语言使用的是值传递

---

函数定义后可作为另外一个函数的实参数传入

### 闭包

**闭包的概念:** 是可以包含自由（未绑定到特定对象）变量的代码块，这些变量不在这个代码块内或者任何全局上下文中定义，而是在定义代码块的环境中定义。要执行的代码块（由于自由变量包含在代码块中，所以这些自由变量以及它们引用的对象没有被释放）为自由变量提供绑定的计算环境（作用域）。

**闭包的价值:** 闭包的价值在于可以作为函数对象或者匿名函数，对于类型系统而言，这意味着不仅要表示数据还要表示代码。支持闭包的多数语言都将函数作为第一级对象，就是说这些函数可以存储到变量中作为参数传递给其他函数，最重要的是能够被函数动态创建和返回。

Go语言中的闭包同样也会引用到函数外的变量。闭包的实现确保只要闭包还被使用，那么被闭包引用的变量会一直存在。

**总结:** 闭包并不是一门编程语言不可缺少的功能，但闭包的表现形式一般是以匿名函数的方式出现，就象上面说到的，能够动态灵活的创建以及传递，体现出函数式编程的特点。所以在一些场合，我们就多了一种编码方式的选择，适当的使用闭包可以使得我们的代码简洁高效。

**闭包的注意点:** 
- 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包

- 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。


以下实例中，我们创建了函数 getSequence() ，返回另外一个函数。该函数的目的是在闭包中递增 i 变量，代码如下：
```go
package main

import "fmt"

func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1())
   fmt.Println(nextNumber1())
}
```
输出：

    1
    2
    3
    1
    2

### 方法

方法在func关键字后是**接收者**而不是函数名，接收者可以是自己定义的一个类型，这个类型可以是struct，interface，甚至我们可以重定义基本数据类型。

    func (variable_name variable_data_type) function_name() [return_type]{
    /* 函数体*/
    }

```go
package main

import (
   "fmt"  
)

/* 定义结构体 */
type Circle struct {
  radius float64
}

func main() {
  var c1 Circle
  c1.radius = 10.00
  fmt.Println("Area of Circle(c1) = ", c1.getArea())
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
  //c.radius 即为 Circle 类型对象中的属性
  return 3.14 * c.radius * c.radius
}
```

## go语言变量作用域

Go 语言中变量可以在三个地方声明：

- 函数内定义的变量称为局部变量
- 函数外定义的变量称为全局变量
- 函数定义中的变量称为形式参数
  
```go
package main

import (
	"fmt"
)

func main() {
	var a int = 10
	fmt.Println(a)
	{
		a := 9
		fmt.Println(a)
		a = 8
	}
	fmt.Println(a)

}
```
输出：

    10
    9
    10

## go语言数组

### 声明数组

Go 语言数组声明需要指定元素类型及元素个数，语法格式如下：

    var variable_name [SIZE] variable_type
    //多维
    var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type

### 初始化数组

初始化数组中 {} 中的元素个数不能大于 [] 中的数字。

如果忽略 [] 中的数字不设置数组大小，Go 语言会根据元素的个数来设置数组的大小：

    var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
    //多维
    a = [3][4]int{  
    {0, 1, 2, 3} ,   /*  第一行索引为 0 */
    {4, 5, 6, 7} ,   /*  第二行索引为 1 */
    {8, 9, 10, 11},   /* 第三行索引为 2 */
    }

### 访问数组
数组元素可以通过索引（位置）来读取。格式为数组名后加中括号，中括号中为索引的值。例如：

    var salary float32 = balance[9]

```go
package main

import "fmt"

func main() {
   var n [10]int /* n 是一个长度为 10 的数组 */
   var i,j int

   /* 为数组 n 初始化元素 */        
   for i = 0; i < 10; i++ {
      n[i] = i + 100 /* 设置元素为 i + 100 */
   }

   /* 输出每个数组元素的值 */
   for j = 0; j < 10; j++ {
      fmt.Printf("Element[%d] = %d\n", j, n[j] )
   }
}
```

## go语言指针

Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。

    var ip *int        /* 指向整型*/
    var fp *float32    /* 指向浮点型 */

### 空指针
当一个指针被定义后没有分配到任何变量时，它的值为 nil。

nil 指针也称为空指针。

nil在概念上和其它语言的null、None、nil、NULL一样，都指代零值或空值。

一个指针变量通常缩写为 ptr。


### 指针数组

```go
package main

import "fmt"

const MAX int = 3

func main() {
   a := []int{10,100,200}
   var i int
   var ptr [MAX]*int;

   for  i = 0; i < MAX; i++ {
      ptr[i] = &a[i] /* 整数地址赋值给指针数组 */
   }

   for  i = 0; i < MAX; i++ {
      fmt.Printf("a[%d] = %d\n", i,*ptr[i] )
   }
}
```

- 指针可以指向指针
  
        var ptr **int;

- 指针可以当做函数变量
    ```go
    package main

    import "fmt"

    func main() {
    /* 定义局部变量 */
    var a int = 100
    var b int= 200

    fmt.Printf("交换前 a 的值 : %d\n", a )
    fmt.Printf("交换前 b 的值 : %d\n", b )

    /* 调用函数用于交换值
    * &a 指向 a 变量的地址
    * &b 指向 b 变量的地址
    */
    swap(&a, &b);

    fmt.Printf("交换后 a 的值 : %d\n", a )
    fmt.Printf("交换后 b 的值 : %d\n", b )
    }

    func swap(x *int, y *int) {
    var temp int
    temp = *x    /* 保存 x 地址的值 */
    *x = *y      /* 将 y 赋值给 x */
    *y = temp    /* 将 temp 赋值给 y */
    }
    ```

## go语言结构体

### 定义和声明

```go
package main

import "fmt"

type Member struct {
	name string
	age  int
}

func main() {

	// 创建一个新的结构体
	fmt.Println(Member{"Go 语言", 10})

	// 也可以使用 key => value 格式
	fmt.Println(Member{name: "Go 语言", age: 10})

	// 忽略的字段为 0 或 空
	fmt.Println(Member{name: "Go 语言"})
}
```
输出：

    {Go 语言 10}
    {Go 语言 10}
    {Go 语言 0}

### 访问结构体

如果要访问结构体成员，需要使用点号 . 操作符

### 结构体作为函数参数

```go
package main

import "fmt"

type Member struct {
	name string
	age  int
}


func main() {

	// 创建一个新的结构体
	one := Member{"Go", 10}
    two := Member{"python", 20}
    
    var three Member
    three.name = "c++"
    three.age = 30

    printMember(one)

    printMember(two)

    printMember(three)

}

func printMember( member Member ) {
   fmt.Printf( "name : %s\n", member.name)
   fmt.Printf( "age : %d\n", member.age)

}
```
输出：

    name : Go
    age : 10
    name : python
    age : 20
    name : c++
    age : 30

### 结构体指针

在Go语言中，直接砍掉了最复杂的指针运算的部分，只留下了获取指针（&运算符）和获取对象（*运算符）的运算。

对于一些复杂类型的指针， 如果要访问成员变量的话，需要写成类似(*p).field的形式，Go提供了隐式解引用特性，我们只需要p.field即可访问相应的成员。

```go
package main

import "fmt"

type Member struct {
	name string
	age  int
}

func main() {

	var three Member
	three.name = "c++"
	three.age = 30

	var struct_pointer *Member
	struct_pointer = &three
	fmt.Printf("name : %s\n", struct_pointer.name)
	fmt.Printf("name : %s\n", (*struct_pointer).name)
}
```
输出：

    name : c++
    name : c++



## go语言切片

### 定义切片
- 一个未指定大小的数组来定义切片（切片不需要说明长度）：

        var identifier []type


- 使用make()函数来创建切片:

        var slice1 []type = make([]type, len)

        也可以简写为

        slice1 := make([]type, len)
- 指定容量，其中capacity为可选参数。

        make([]T, length, capacity)

### 切片初始化
 
- 直接初始化切片，[]表示是切片类型，{1,2,3}初始化值依次是1,2,3.其cap=len=3

        s :=[] int {1,2,3 }

- 初始化切片s,是数组arr的引用

        s := arr[:] 

    - 将arr中从下标startIndex到endIndex-1 下的元素创建为一个新的切片[左闭右开）

            s := arr[startIndex:endIndex] 

    - 默认 endIndex 时将表示一直到arr的最后一个元素

            s := arr[startIndex:] 


    - 默认 startIndex 时将表示从arr的第一个元素开始

            s := arr[:endIndex] 

- 通过切片s初始化切片s1

        s1 := s[startIndex:endIndex]

- 通过内置函数make()初始化切片s,[]int 标识为其元素类型为int的切片
        
        s :=make([]int,len,cap) 

### 切片常用方法
- len() 切片changdu
- cap() 切片最长容量
- append() 添加元素

        /* 添加一个元素 */
        numbers = append(numbers, 1)

        /* 同时添加多个元素 */
        numbers = append(numbers, 2,3,4)

- copy() 

        /* 创建切片 numbers1 是之前切片的两倍容量*/
        numbers1 := make([]int, len(numbers), (cap(numbers))*2)

        /* 拷贝 numbers 的内容到 numbers1 */
        copy(numbers1,numbers)

### Tips
- slice 本身没有数据，是对底层数组的一个view。
- slice 本质是 切片头元素指针 + len + cap。
- slice支持向后拓展，不支持向前。

        arr := [...]int{0,1,2,3,4,5,6,7}
        s := arr[2:6]
        s1 := s[3:5]
        //s = [2,3,4,5]
        //s1 = [5,6]
    s[i] 不可以超越len(s)，向后拓展不可超过cap(s)
- 添加元素超过cap，将会分配更大的底层数组。
- 空(nil)切片, 一个切片在未初始化之前默认为 nil，长度为 0，cap为 0。

## go语言Range
 **range** 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对。

```go
package main
import "fmt"
func main() {
    //这是我们使用range去求一个slice的和。使用数组跟这个很类似
    nums := []int{2, 3, 4}
    sum := 0
    for _, num := range nums {
        sum += num
    }
    fmt.Println("sum:", sum)
    //在数组上使用range将传入index和值两个变量。上面那个例子我们不需要使用该元素的序号，所以我们使用空白符"_"省略了。有时侯我们确实需要知道它的索引。
    for i, num := range nums {
        if num == 3 {
            fmt.Println("index:", i)
        }
    }
    //range也可以用在map的键值对上。
    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }
    //range也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
    for i, c := range "go" {
        fmt.Println(i, c)
    }
}
```

## go语言Map

**Map** 是一种**无序**的键值对的集合。Map 最重要的一点是通过 key 来快速检索数据，key 类似于索引，指向数据的值。

我们可以像迭代数组和切片那样迭代它。不过，Map 是无序的，我们无法决定它的返回顺序，这是因为 Map 是使用 hash 表来实现的。


### 定义 Map
可以使用内建函数 make 也可以使用 map 关键字来定义 Map:

    /* 声明变量，默认 map 是 nil */
    var map_variable map[key_data_type]value_data_type

    var countryCapitalMap map[string]string 
    countryCapitalMap = make(map[string]string)
    countryCapitalMap [ "France" ] = "巴黎"

    /* 使用 make 函数 */
    map_variable := make(map[key_data_type]value_data_type)

    countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome"}

如果不初始化 map，那么就会创建一个 nil map。nil map 不能用来存放键值对

### delete

delete() 函数用于删除集合的元素, 参数为 map 和其对应的 key。

    delete(countryCapitalMap, "France")

## go语言类型转换

类型转换用于将一种数据类型的变量转换为另外一种类型的变量。Go 语言类型转换基本格式如下：

    type_name(expression)
    //type_name 为类型，expression 为表达式

