---
title: go
urlname: go
top: True
categories:
  - 编程语言
tags:
  - go
author: jesonlin
date: 2022-12-13 18:23:30
update: 2022-12-13 18:23:30
---

Go 是由谷歌工程师 Robert Griesemer、Rob Pike 和 Ken Thompson 设计的。它是一种静态类型的、编译的语言。

<!-- more -->

## 安装
1. centos-yum
    ```
    yum install golang
    ```
2. centos-tar
    ```
    <!-- 下载 -->
    https://go.dev/doc/install
    <!-- 解压 -->
    rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.4.linux-amd64.tar.gz
    <!-- 添加环境变量 -->
    vim /etc/profile
    export PATH=$PATH:/usr/local/go/bin
    <!-- 重启终端查看go版本 -->
    go version
    ```

## hello, word
1. Go 是由 packages（包）组成的。  
    vim main.go  
    ```
    package main  # 程序入口点，package main 告诉 Go 编译器，该程序被编译为可执行文件，而不是共享库。

    import (
     "fmt"
    )

    func main(){
      fmt.Println("Hello World!")
    }
    ```
2. 执行
    ```
    go build main.go
    ./main
    go run main.go
    ```

## 基础
1. 变量  
    Go 中的变量是明确声明的。Go 是一种静态类型的语言。这意味着在声明变量的时候会检查变量的类型。
    ```
    var a int  // 声明a为int型，并附初始值0
    var a = 1  // 等价于 var a int = 1 
    var a, b int = 1, 2
    a := 1  // 只能在函数内部
    ```
2. 整型、浮点型、无符号整型、字符串、布尔
   ```
   var e int = 1  // 整型：int, int8, int16, int32, int64
   var d float32 = 3.14  // 浮点数：float32 和 float64
   var c uint = 1  //无符号整型：uint, uint8, uint16, uint32, uint64, uintptr
   var b string = 'hello'  // 字符串：string
   var a bool = true  // 布尔：bool
   ```
3. 数组、切片、maps
    ```
    var a [5]int  // 一维数组
    var multiA [2][3]int  // 二维数组

    var b []int  // 切片容量为 0、长度为 0 的切片。
    numbers := make([]int, 5, 10)  // 初始长度为 5，容量为 10。
    numbers = append(numbers, 1, 2, 3, 4)  // append 函数将值添加到数组的末端
    ```
4. maps
    ```
    var m map[string]int
    m['xx'] = 2
    ```
5. 类型转换
    ```
    a := 1.1
    b := int(a)
    ```
6. 条件语句
    1. if else
        ```
        if num := 9; num < 0 {
            fmt.Println(num, "is negative")
        } else if num < 10 {
            fmt.Println(num, "has 1 digit")
        } else {
            fmt.Println(num, "has multiple digits")
        }
        ```
    2. switch case
        ```
        i := 2
        switch i {
            case 1:
                fmt.Println("one")
            case 2:
                fmt.Println("two")
            default:
                fmt.Println("none")
        }
        ```
7. 循环
    ```
    i := 0
    sum := 0
    for i := 0; i < 10; i++ {  // 三个条件都可以去掉
        sum += i
    }
    fmt.Println(sum)
    ```
8. 指针  
    在传递结构体作为参数时，或者在为定义的类型声明方法时，通常倾向于使用指针。  
    传递值时，实际上是在复制值，这意味着更多的内存。  
    通过指针，函数改变的值会反映在 方法/函数 调用者身上。  
    ```
    a := 12
    var ap *int
    ap = &a
    var ap *int = &a
    ```
9. 函数
    ```
    func add(a int, b int ) (c int, s string) {
        c = a + b
        s = "succ"
        return  // return c, s
    }
    func add(a int, b int ) (int, string) {  // 同时有或者没有变量名
        c := a + b
        s := "succ"
        return  return c, s
    }
    funcmain() {
        sum, msg := add(2, 1)
        fmt.Println(sum, msg)
    }
    ```
10. 结构体、方法、接口
    Go并不是一种完全面向对象的语言，但通过结构体（Struct）、接口（Interface）和方法（Method），它有很多面向对象的支持和感觉。  
    1. 结构体：结构体是一种类型化的、不同字段的集合。结构体用于将数据分组
        ```
        <!-- 定义 -->
        type person struct {  // 有序
            name string
            age int
            gender string
        }
        <!-- 创建 -->
        p := person{name: "Bob", age: 12, gender: "Male"}
        p := person{"Bob", 12, "Male"}
        <!-- 访问 -->
        fmt.Println(p.name, p.age, p.gender)
        <!-- 指针访问 -->
        p := &person{name: "Bob", age: 12, gender: "Male"}
        fmt.Println(p.name, p.age, p.gender)
        ```
    2. 方法：方法（Method）是一种特殊的函数类型，它有一个 receiver 。receiver 可以是一个值或一个指针。
        ```
        func (p *person) describe() {
            fmt.Printf("%v is %v years old.\n", p.name, p.age)
        }
        func (p *person) setAge(age int) {
            p.age = age
        }
        func (p person) setName(name string) {  // receiver是结构体值，并不是指针，所以是拷贝修改，不影响调用体
            p.name = name
        }
        p := &person{name: "Bob", age: 12, gender: "Male"}
        p.describe()
        p.setAge(66)
        fmt.Println(p.age)
        p.setName("jesonlin")
        fmt.Println(p.name)
        ```



## REFERENCE
[go官网](https://golang.org )
[go在线运行](https://go.dev/play/)
[从 0 到 1 学习 Golang](https://www.freecodecamp.org/chinese/news/learning-go-from-zero-to-hero/)