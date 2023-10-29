---
title: GO语言的一些特点
abbrlink: 276316860
date: 2023-04-05 17:34:12
tags: Go
---

> golang 学习文档 [文档官网](https://golang.halfiisland.com/guide.html)

# 1. 格式

Go 语言的一些格式不能改变，改变后就会报错。

比如：

- 1. {}

函数体、for 循环体等需要使用`{}`的，左括号不能另起一行。

正确的格式是：

```
func main {

}
```

错误的格式是：

```
func main
{

}
```

- 2. 变量初始化

`var num int = 5` 与 `num := 5` 一样。

在进行`var num int`之后，不能使用`num :=`，因为`:=`也是初始化（并赋值）的意思。

---

# 2.break、continue 的高级用法

break 语句可以结束 for、switch 和 select 的代码块。

break 语句还可以在语句后面添加标签 XX（即下面代码中的 BreakTag、BreakTag2），表示退出某个标签对应的代码块。

标签要求必须定义在对应的 for、switch 和 select 的代码块上。

- 一般来说，switch 中无需写 break，默认存在 break。

```
BreakTag:
  for i := 0; i < 5; i++ {
    BreakTag2:
      for j := 0; j < 4; j++ {
        fmt.Println(i, j)
        switch i {
          case 1:
            break BreakTag
          case 3:
            break BreakTag2
        }
      }
  }
```

打印结果：

```
0 0
0 1
0 2
0 3
#缺少 0 4  因为break跳出了BreakTag2循环
1 0
#缺少 1 1   1 2 ..... 因为break跳出了BreakTag循环
```

---

continue 与 break 类似。

continue 语句可以结束当前循环，开始下一次的循环迭代过程，仅限在**for 循环内**使用。在 continue 语句后添加标签时，表示开始标签对应的循环.

```
ContinueTag:
  for i := 0; i < 5; i++ {
    ContinueTag2:
      for j := 0; j < 4; j++ {
        fmt.Println(i, j)
        switch i {
          case 1:
            break ContinueTag
          case 3:
            break ContinueTag2
        }
      }
  }
```

打印结果：

```
0 0
0 1
0 2
0 3
1 0
```

---

# 3.自定义函数的参数的数量可变

函数内部处理可变参数的时候，将可变参数当作切片来处理。

举个例子：

```
package main
import (
  "fmt"
)
// 定义一个函数，函数的参数为可变参数 ... 参数的数量可变
// args...int 表示可以传入任意多个数量的int类型的数据 传入0个，1个 …… n个
func myFunc(args...int) {
  // 遍历可变参数：
  for i:=0;i<len(args);i++{
    fmt.Println(args[i])
  }
}

func main() {
  myFunc()
  myFunc(1, 2)
  myFunc(1, 2, 3, 4, 5)
}
```

---

# 4. 自定义函数能够改变函数外的值

## 默认是不会影响原来的值

我们都知道，基本数据类型和数组默认都是值传递的，即进行值拷贝。在函数内修改，不会影响到原来的值。

例如：

```
package main
import (
  "fmt"
)
func test(num int) {
  num = 30
  fmt.Println("test----",num) // 结果为30
}
func main() {
  var num int = 10
  test(num)
  fmt.Println("main----",num) // 结果为10
}
```

上述例子中函数 test 改变了 num 的值为 30，但是 test 函数内的 num 的内存地址是在 test 函数区域内（这片内存空间我们称为函数 test 的栈帧），main 函数中的 num 的地址是在 main 函数区域内，两个 num 的地址空间不同，所以 main 中的 num 的值不变。

栈一般存放基本数据类型；堆一般存放引用数据类型；代码区存放代码本身。

- 那么我们如果想要修改函数外的值怎么办呢？

## 影响函数外的值

以值传递方式的数据类型，如果希在函数内的变量能修改函数外的变量，可以传入变量的地址&，函数内以指针的方式操作变量。从效果来看类似引用传递。

```
package main
import (
  "fmt"
)
func test(num *int) {
  *num = 30 // 对该地址对应的变量进行改变数值
}
func main() {
  var num int = 10
  fmt.Println(&num) // 结果为num的地址，例如是：0xc0000100b0
  test(&num) // 传入的是num的地址
  fmt.Println(num) // 结果为30
}
```

---

# 5. 函数与数据类型

## 5.1 函数也可作为一个数据类型

函数可以赋值给一个变量，则该变量就是一个函数类型的变量了。通过该变量可以对函数调用。

```
package main
import (
  "fmt"
)
func test(num int) {
  fmt.Println(num)
}
func main() {
  a := test
  fmt.Printf("a的类型是：%T,test函数的类型是：%T\n",a,test)
  // 输出结果：a的类型是:func(int),test函数的类型是:func(int)
  a(10) // 等价于 test(10)
}
```

## 5.2 函数可以作为形参

函数既然是一种数据类型，因此在 Go 中，函数可以作为形参，并且调用。

在 5.1 代码中加入/修改下列代码:

```
// 加入的
func test02(num1 int, num2 float32, testFunc func(int)) {
  fmt.Println("test02---")
}
// 修改的
func main() {
  a := test
  // 调用test02函数
  test02(10,3.14,test)
  test02(1,2.1,a)
}
// 程序运行不报错
```

## 5.3 支持自定义数据类型

基本语法为：`type [自定义数据类型名] [数据类型]`

可以理解为：**相当于起了一个别名**

例如：

```
type myInt int

var num1 myInt = 30
fmt.Println("num1",num1)

var num2 int = 30

// num2 = num1  // 错误的，虽然是别名，但是在go中编译识别的时候还是认为myInt和int不是一种数据类型

num2 = int(num1) // 强转一下就行了。

fmt。Println("num2",num2)
```

> 同理，如果我们将 int 换为 func(int,int) int,这时的 myInt 就等价一个函数类型 func(int,int) int

## 5.4 支持对函数返回值命名

当我们为函数返回值命名时，就不用写 return 了，，但是函数体内必须存在该返回值。

> 我们传统的写法，return 时必须将返回值一一对应，前后不能调换

例如：

```
// 传统写法
fun test(num1 int, num2 int)(int,int){
  result1 := num1 + num2
  result2 := num1 - num2
  return result1,result2
}
```

> 给返回值命名后，则不用写 return 了，这样顺序就无所谓了

例如：

```
// 给返回值命名
fun test(num1 int, num2 int)(sum int,sub int){
  sum := num1 + num2
  sub := num1 - num2
  return
}
```

---

# 跳过的一些知识点

> 6. init 函数
> 7. 匿名函数
> 8. 闭包
> 9. defer 关键字

---

# 10.系统函数

> 参考链接 1：[Golang 中文学习文档](https://golang.halfiisland.com/%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8/%E6%A0%87%E5%87%86%E5%BA%93/)
> 参考链接 2：[Golang 标准库文档](http://doc.golang.ltd/)

## 10.1 字符串相关函数

### 10.1.1 字符串长度

统计字符串的长度，按字节进行统计。

使用：`len(str)`

返回值类型：`int`

- Go 中汉字是 utf-8 编码，所以 1 个汉字是 3 个字节

### 10.1.2 字符串遍历

- 方式 1：`for-range键值循环`

```
str := "golang你好"
for i, value := range str {
  fmt.Printf("索引是：%d，具体的值为：%c \n",i,value)
}
```

**%c，输出字符**

- 方式 2：字符串转为切片——`r:=[]rune(str)`

```
str := "golang你好"
r := []rune(str)
for i := 0; i < len(r); i++ {
  fmt.Println(r[i])
}
```

上例输出的是 字符对应的字节

改为`fmt.Printf("%c \n",r[i])` , 就输出字符了。

### 10.1.3 字符串与整数互转

- 字符串转整数

```
import (
  ...
  "strconv"
)

...
// 此处error可以替换为 _
num,error := strconv.Atoi("666")
// num为int型
...

```

- 整数转字符串

```
import (
  ...
  "strconv"
)

...
// 此处error可以替换为 _
str := strconv.Itoa(80)
// str为string型
...

```

### 10.1.4 查找子串是否在指定的字符串中

```
import (
  "strings"
)

isHas := strings.Contains("javaandgolang","go")
// isHas为true
```

### 10.1.5 统计一个字符串有几个指定的字串

```
count := strings.Count("golangandjavaga","ga")
// count为2
```

## 10.2 内置函数

自带函数，无需引入包就能使用。

`package builtin`这个不用写，里边都是内置函数。
