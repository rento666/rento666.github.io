---
title: go每日一题
tags: Go
abbrlink: 1042076231
date: 2023-03-22 20:57:05
---

> 这里写一些 GO 语言的题目。可能面试中会用到

# 1.闭包问题

> 原文链接：[https://studygolang.com/interview/question](https://studygolang.com/interview/question)

这是[GO 语言爱好者周刊第 90 期](https://studygolang.com/topics/13470)的一道题目。以下代码输出什么？

```
package main

import "fmt"

func app() func(string) string {
	t := "Hi"
	c := func(b string) string {
		t = t + " " + b
		return t
	}
	return c
}

func main() {
	a := app()
	b := app()
	a("go")
	fmt.Println(b("All"))
}
```

`A：Hi All；B：Hi go All；C：Hi；D：go All`
