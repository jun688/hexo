---
title: 使用var关键字
tags:
  - go
originContent: "# 使用var关键字\n## 1.变量定义\n  #### 1) 使用var关键字\n    var a,b,c bool \n    var s1,s2 string = \"hello\", \"world\"\n    可放在函数内，或直接放在包内\n    使用var()集中定义变量\n\n  #### 2) 让编译器自动决定类型\n     var a,b,i,s1,s2 = true, false, 3, \"hello\", \"world\"\n\n  #### 3) 使用:=定义变量\n    a,b,i,s1,s2 := true, false, 3, \"hello\", \"world\"\n    只能在函数内使用\n    \n```\npackage main\n\nimport \"fmt\"\n\nvar (\n\taa = 33\n\tbb = \"kk\"\n\tss = true\n)\n\nfunc variableZeroValue() {\n\tvar a int\n\tvar s string\n\tfmt.Printf(\"%d %q\\n\", a, s)\n}\n\nfunc variableInitValue() {\n\tvar a, b int = 3, 4\n\tvar s string = \"abc\"\n\tfmt.Println(a, b, s)\n}\n\nfunc variableTypeDeduction()  {\n\tvar a, b, c, s = 3, 4, true, \"def\"\n\tfmt.Println(a, b, c, s)\n}\n\nfunc variableShorter() {\n\ta, b, c, s := 3, 4, true, \"def\"\n\tfmt.Println(a, b, c, s)\n}\n\nfunc main() {\n\tfmt.Println(\"Hello, world\")\n\tvariableZeroValue()\n\tvariableInitValue()\n\tvariableTypeDeduction()\n\tvariableShorter()\n\tfmt.Println(aa, bb, ss)\n}\n```"
categories:
  - go
toc: true
date: 2018-12-04 19:47:54
---

## 1.变量定义
  #### 1) 使用var关键字
    var a,b,c bool 
    var s1,s2 string = "hello", "world"
    可放在函数内，或直接放在包内
    使用var()集中定义变量

  #### 2) 让编译器自动决定类型
     var a,b,i,s1,s2 = true, false, 3, "hello", "world"

  #### 3) 使用:=定义变量
    a,b,i,s1,s2 := true, false, 3, "hello", "world"
    只能在函数内使用
    
```
package main

import "fmt"

var (
	aa = 33
	bb = "kk"
	ss = true
)

func variableZeroValue() {
	var a int
	var s string
	fmt.Printf("%d %q\n", a, s)
}

func variableInitValue() {
	var a, b int = 3, 4
	var s string = "abc"
	fmt.Println(a, b, s)
}

func variableTypeDeduction()  {
	var a, b, c, s = 3, 4, true, "def"
	fmt.Println(a, b, c, s)
}

func variableShorter() {
	a, b, c, s := 3, 4, true, "def"
	fmt.Println(a, b, c, s)
}

func main() {
	fmt.Println("Hello, world")
	variableZeroValue()
	variableInitValue()
	variableTypeDeduction()
	variableShorter()
	fmt.Println(aa, bb, ss)
}
```