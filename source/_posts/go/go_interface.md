---
title:  Go_接口
categories: [杂记]
tags:   [Go, Note]
copyright: false``
reward: false
rating: false
related_posts: false
date: 2020-03-9 04:51:55
---

## go语言接口

在Go语言中接口（interface）是一种**类型**，一种抽象的类型。

interface是一组method的集合，接口做的事情就像是定义一个协议（规则），不关心属性（数据），只关心行为（方法）。


### 接口的定义
Go语言提倡面向接口编程。

每个接口由数个方法组成，接口的定义格式如下：

    type 接口类型名 interface{
        方法名1( 参数列表1 ) 返回值列表1
        方法名2( 参数列表2 ) 返回值列表2
        …
    }


- 接口名：使用type将接口定义为自定义的类型名。接口名最好要能突出该接口的类型含义。
- 方法名：当方法名首字母是大写且这个接口类型名首字母也是大写时，这个方法可以被接口所在的包（package）之外的代码访问。
- 参数列表、返回值列表：参数列表和返回值列表中的参数变量名可以省略。


        type writer interface{
            Write([]byte) error
        }


### 实现接口的条件
一个对象只要全部实现了接口中的方法，那么就实现了这个接口。换句话说，接口就是一个需要实现的方法列表。

```go
// Sayer 接口
type Sayer interface {
	say()
}
//定义dog和cat两个结构体：

type dog struct {}

type cat struct {}
//因为Sayer接口里只有一个say方法，所以我们只需要给dog和cat 分别实现say方法就可以实现Sayer接口了。

// dog实现了Sayer接口
func (d dog) say() {
	fmt.Println("汪汪汪")
}

// cat实现了Sayer接口
func (c cat) say() {
	fmt.Println("喵喵喵")
}

```
接口的实现就是这么简单，只要实现了接口中的所有方法，就实现了这个接口。

### 接口类型变量

接口类型变量能够存储所有实现了该接口的实例。 例如上面的示例中，Sayer类型的变量能够存储dog和cat类型的变量。

```go
func main() {
	var x Sayer // 声明一个Sayer类型的变量x
	a := cat{}  // 实例化一个cat
	b := dog{}  // 实例化一个dog
	x = a       // 可以把cat实例直接赋值给x
	x.say()     // 喵喵喵
	x = b       // 可以把dog实例直接赋值给x
	x.say()     // 汪汪汪
}
```

### 值接收者和指针接收者实现接口的区别
使用值接收者实现接口和使用指针接收者实现接口有什么区别呢？

```go
//我们有一个Mover接口和一个dog结构体。

type Mover interface {
	move()
}

type dog struct {}

//值接收者实现接口
func (d dog) move() {
	fmt.Println("狗会动")
}

//此时实现接口的是dog类型：

func main() {
	var x Mover
	var wangcai = dog{} // 旺财是dog类型
	x = wangcai         // x可以接收dog类型
	var fugui = &dog{}  // 富贵是*dog类型
	x = fugui           // x可以接收*dog类型
	x.move()
}
```
从上面的代码中我们可以发现，使用值接收者实现接口之后，不管是dog结构体还是结构体指针*dog类型的变量都可以赋值给该接口变量。因为Go语言中有对指针类型变量求值的语法糖，dog指针fugui内部会自动求值*fugui。

```go
//指针接收者实现接口
func (d *dog) move() {
	fmt.Println("狗会动")
}
func main() {
	var x Mover
	var wangcai = dog{} // 旺财是dog类型
	x = wangcai         // x不可以接收dog类型
	var fugui = &dog{}  // 富贵是*dog类型
	x = fugui           // x可以接收*dog类型
}
```
此时实现Mover接口的是*dog类型，所以不能给x传入dog类型的wangcai，此时x只能存储*dog类型的值。


样例:

```go
package main

import "fmt"

type People interface {
	Speak(string) string
}

type Student struct{}

func (stu *Student) Speak(think string) (talk string) {
	if think == "sb" {
		talk = "你是个大帅比"
	} else {
		talk = "您好"
	}
	return
}

func main() {
	//wrong
	//var peo People = Student{}
	//true
	var peo People = &Student{}

	think := "bitch"
	fmt.Println(peo.Speak(think))
}
```

### 类型与接口的关系
- 一个类型可以同时实现多个接口，而接口间彼此独立，不知道对方的实现。 例如，狗可以叫，也可以动。我们就分别定义Sayer接口和Mover接口，如下： Mover接口。

    ```go
    // Sayer 接口
    type Sayer interface {
        say()
    }

    // Mover 接口
    type Mover interface {
        move()
    }
    //dog既可以实现Sayer接口，也可以实现Mover接口。

    type dog struct {
        name string
    }

    // 实现Sayer接口
    func (d dog) say() {
        fmt.Printf("%s会叫汪汪汪\n", d.name)
    }

    // 实现Mover接口
    func (d dog) move() {
        fmt.Printf("%s会动\n", d.name)
    }

    func main() {
        var x Sayer
        var y Mover

        var a = dog{name: "旺财"}
        x = a
        y = a
        x.say()
        y.move()
    }
    ```

- 多个类型实现同一接口, Go语言中不同的类型还可以实现同一接口 首先我们定义一个Mover接口，它要求必须由一个move方法。
    ```go
    // Mover 接口
    type Mover interface {
        move()
    }
    //例如狗可以动，汽车也可以动，可以使用如下代码实现这个关系：

    type dog struct {
        name string
    }

    type car struct {
        brand string
    }

    // dog类型实现Mover接口
    func (d dog) move() {
        fmt.Printf("%s会跑\n", d.name)
    }

    // car类型实现Mover接口
    func (c car) move() {
        fmt.Printf("%s速度70迈\n", c.brand)
    }
    
    //这个时候我们在代码中就可以把狗和汽车当成一个会动的物体来处理了，不再需要关注它们具体是什么，只需要调用它们的move方法就可以了。

    func main() {
        var x Mover
        var a = dog{name: "旺财"}
        var b = car{brand: "保时捷"}
        x = a
        x.move()
        x = b
        x.move()
    }
    ```
    执行结果：

        旺财会跑  
        保时捷速度70迈


### 接口嵌套
接口与接口间可以通过嵌套创造出新的接口。
```go
// Sayer 接口
type Sayer interface {
	say()
}

// Mover 接口
type Mover interface {
	move()
}

// 接口嵌套
type animal interface {
	Sayer
	Mover
}
//嵌套得到的接口的使用与普通接口一样，这里我们让cat实现animal接口：

type cat struct {
	name string
}

func (c cat) say() {
	fmt.Println("喵喵喵")
}

func (c cat) move() {
	fmt.Println("猫会动")
}

func main() {
	var x animal
	x = cat{name: "花花"}
	x.move()
	x.say()
}
```

### 空接口

空接口是指没有定义任何方法的接口。因此任何类型都实现了空接口。

- 空接口类型的变量可以存储任意类型的变量。
        
    ```go
    func main() {
        // 定义一个空接口x
        var x interface{}
        s := "Hello"
        x = s
        fmt.Printf("type:%T value:%v\n", x, x)
        i := 100
        x = i
        fmt.Printf("type:%T value:%v\n", x, x)
        b := true
        x = b
        fmt.Printf("type:%T value:%v\n", x, x)
    }
    ```

- 空接口作为函数的参数,使用空接口实现可以接收任意类型的函数参数。
        
    ```go
    // 空接口作为函数参数
    func show(a interface{}) {
        fmt.Printf("type:%T value:%v\n", a, a)
    }
    ```
- 空接口作为map的值，使用空接口实现可以保存任意值的字典。
    ```go    
    // 空接口作为map值
        var studentInfo = make(map[string]interface{})
        studentInfo["name"] = "无恙"
        studentInfo["age"] = 18
        studentInfo["married"] = false
        fmt.Println(studentInfo)
    ```

### 类型断言
空接口可以存储任意类型的值，那我们如何获取其存储的具体数据呢？

    一个接口的值（简称接口值）是由 **一个具体类型** 和 **具体类型的值** 两部分组成的。这两部分分别称为接口的 **动态类型** 和 **动态值**。

想要判断空接口中的值这个时候就可以使用类型断言，其语法格式：

    x.(T)
其中：

    x：表示类型为interface{}的变量
    T：表示断言x可能是的类型。

    该语法返回两个参数，第一个参数是x转化为T类型后的变量，第二个值是一个布尔值，若为true则表示断言成功，为false则表示断言失败。

样例:
```go
package main

import "fmt"

func main() {
	var x interface{}
	x = "Hello"
    //wrong
    //v, ok := x.(int)
    //true
    v, ok := x.(string)

	if ok {
		fmt.Println(v)
	} else {
		fmt.Println("类型断言失败")
	}
}
```

上面的示例中如果要断言多次就需要写多个if判断，这个时候我们可以使用switch语句来实现：

```go
func justifyType(x interface{}) {
	switch v := x.(type) {
	case string:
		fmt.Printf("x is a string，value is %v\n", v)
	case int:
		fmt.Printf("x is a int is %v\n", v)
	case bool:
		fmt.Printf("x is a bool is %v\n", v)
	default:
		fmt.Println("unsupport type！")
	}
}
```

因为空接口可以存储任意类型值的特点，所以空接口在Go语言中的使用十分广泛。
