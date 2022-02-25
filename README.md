# EffectiveGo![](https://visitor-badge.glitch.me/badge?page_id=kerthcet.EffectiveGo)

## 目录表
- [介绍](#介绍)
  - [示例](#示例)
- [格式化](#格式化)

## 介绍
Go 是一门全新的语言。虽然它从其他语言中借鉴了一些思想，但 Go 与众不同的特性，使得它与其他同类型语言在本质上又有不同。将 C++ 或者 Java 程序直译成 Go 语言无法令我们满意，毕竟 Java 程序是使用 Java 语言编写的，而不是 Go 语言。另外，从 Go 语言的角度去思考问题，同样能编写出成功运行但实现却大为不同的代码。换句话说，为了能更把 Go 程序写的更好，掌握它的特性和代码风格是极其重要的。了解 Go 语言命名，格式化，程序构造等既定的规则也同样重要，只有这样，才利于你写出对其他 Go 语言程序员友好的代码。

这篇文章就如何写出清晰，地道的 Go 代码给出了一些建议。 在你读本文之前，希望你已经看过 [Go 语言规范](https://go.dev/ref/spec)，[Go 语言之旅](https://go.dev/tour/welcome/1)，[如何使用 Go 编程](https://go.dev/doc/code)。

2022年备注：本文于2009年为 Go 发版而写，自此并没有引入大的改动。虽然这是一份关于如何使用 Go 语言很好的指南，但是它很少提及具体的函数库。另外感谢 Go 语言的稳定性，自从它诞生以来，包括编译系统，测试，模块组件和多态性等都没有发生重大的变化。考虑到现在已经有大量且不断增长的文档、博客和书籍诠释了如何更好的使用 Go 语言，因此本文也并没有更新计划。未来，本文仍然是有价值的，但它绝对不是全部，你可以参考 [issue 28782](https://github.com/golang/go/issues/28782)。

### 示例
[Go 包源码](https://go.dev/src/)不仅存放了核心库，同时也是学习 Go 语言很好的示范。此外，[golang.org](https://go.dev/) 网站提供了大量各个包的可执行示例，你可以在该网站直接运行，比如这个[示例](https://pkg.go.dev/strings#example_Map)（你可以尝试点击 `Example` 展开所有的示例）。如果你有任何疑惑或者实现相关的问题，这些文档，代码和函数库中的示例也许可以给你答案，或者给你提供一些背景和思路。

## 格式化
格式化问题总是充满了争议但是始终没有统一的定论。开发人员可以采用不同的编码风格，但最好不要，如果大家采用相同的代码风格，那么这个话题的争议就会变得更少。问题的关键就在于如何不通过长篇的代码规范来实现这个愿景。

在 Go 语言世界里我们采用了一种不同寻常的方式，让机器来处理大部分格式化问题。`gofmt` 程序（也可以用 `go fmt`，它以包为处理对象而不是源码）会读取 Go 程序，并按照标准风格进行缩进、对齐操作，并在必要的情况下格式化注释。如果你想知道 Go 语言会如何处理一些新的代码布局，运行 `go fmt`；如果结果不如人意，重新组织你的代码程序（或者提交 gofmt 的 bug），不必为这个纠结而花费了很多时间。

举个例子，你不需要手动对齐结构体中字段的注释，`Gofmt` 会为你代劳，假设我们有一个这样的定义：
```golang
type T struct {
    name string // name of the object
    value int // its value
}
```

`gofmt` 会纵向对齐：
```golang
type T struct {
    name    string // name of the object
    value   int    // its value
}
````
所有 Go 语言标准库中的代码已经用 `gofmt` 格式化过了。

下面是一些格式化的细节，很简单：

缩进：

    默认使用制表符（tab）来缩进。你也可以设置为空格（space）

行长：

    Go 没有限制每一行的长度，不要担心太长。如果你感觉一行确实太长了，换行并用 tab 缩进

括号：

    Go 相比于 C 或者 Java 使用了更少的括号：控制结构（`if`，`for`，`switch`）不需要使用括号。此外，运算符优先级也变得更简短和更简洁了，
        x<<8 + y<<16
    这个例子就很好的说明了 Go 语言同其他语言的不通，间距明显变短了。

## 注释
Go 提供了 C 风格的 /* */ 块注释和 C++ 风格的 // 行注释，行注释比较常见，块注释往往用于包的注释，但它在注释文字描述或者一大段代码时还是很好用的。

`godoc` 即可以作为程序也可以作为 web 服务器使用，它可以导出 Go 源码包中的文档。在顶部声明之上，紧邻声明不留空行的注释，会同声明一起导出作为说明文档。这些注释是否写的地道，是否是 Go 语言的风格，决定了 `godoc` 产出的文档质量。

每一个包都应该在包语句之前写上块注释。对于拥有多个文件的包，一个包注释就够了，任何文件都可以做这件事。包注释应该对包做一个介绍，并提供包相关信息。它将在 `godoc` 页面第一个出现，并未后面的内容建立详细的文档。

```golang
/*
Package regexp implements a simple library for regular expressions.

The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term [ '*' | '+' | '?' ]
    term:
        '^'
        '$'
        '.'
        character
        '[' [ '^' ] character-ranges ']'
        '(' regexp ')'
*/
package regexp

```
如果这个包很简单，包注释也可以简单一点。
```golang
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```
注释不需要额外进行格式化，如用星号来突出。由于生成的内容甚至不是以等宽字体显示，所以不要依赖空格对齐，`godoc` 会像 `gofmt` 一样，负责所有的一切。注释是无法解析的纯文本，因此 `HTML` 或者其他注解像 `__this__` 无法使用，他们会原样输出。`godoc` 所做的调整，就是以等宽的字体输出缩进的文本，这对代码片段比较友好。`fmt` 包使用该注释取得了不错的效果。

`godoc` 是否会重新格式化注释取决于上下文，所以请确保他们看起来是清晰的：使用了正确的拼写、标点符号，语句结构和折叠长行等。

在一个包里，任何位于顶部声明之上的注释会被用作声明的文档注释，任何在程序中可导出（大写）的命名都应该有文档注释。

文档注释最好是完整的语句，这样它才能适各种自动显示。注释第一句话应该是单句总结，并且以被声明内容的名字开头。
```golang
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error) {
```
如果所有的文档注释以它自己的名字开头，你就可以使用 [go 工具包](https://pkg.go.dev/cmd/go)中 [doc](https://pkg.go.dev/cmd/go#hdr-Show_documentation_for_package_or_symbol) 相关子命令，配合 `grep` 命令输出。假设你想不起方法名 `Compile` 但是正在寻找正则匹配解析的功能，你就可以运行这个命令：
```
$ go doc -all regexp | grep -i parse
```

如果包内所有的文档注释以“该方法...“开头，`grep` 命令就无法帮助你找到这个方法。但是如果你使用名字作为文档注释的开头，你就可以看到如下你正在找的信息：
```
$ go doc -all regexp | grep -i parse
    Compile parses a regular expression and returns, if successful, a Regexp
    MustCompile is like Compile but panics if the expression cannot be parsed.
    parsed. It simplifies safe initialization of global variables holding
$
```
`Go` 声明语法允许组合申明。一条文档声明语句可以作为一组常量或者变量的介绍。由于是作为一个整体进行声明，注释可以写的笼统一些。
```golang
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = errors.New("regexp: internal error")
    ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
    ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
    ...
)
```

组合声明也可以用来表明组内成员的关系，比如如下声明就表示组内的变量要受到 `mutex` 的保护。
```golang
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```
