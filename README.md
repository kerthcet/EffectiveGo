# EffectiveGo![](https://visitor-badge.glitch.me/badge?page_id=kerthcet.EffectiveGo)

## 目录表
- [介绍](#介绍)
  - [示例](#示例)
- [格式化](#格式化)
- [注释](#注释)
- [命名](#命名)

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

## 命名
命名在 `Go` 语言或其他语言中都是非常重要的。他们甚至有语意上的效果：方法名是否在包外可见是由方法首字母是否大写决定。因此很有必要花一些时间讨论下 `Go` 程序的命名规范。

### 包名
如果在包内导入另一个包，就可以通过包名进行调用。在
```
import "bytes"
```
之后，导入的包就可以调用 `bytes.Buffer`。如果所有人都使用相同的名字来引用其内容，这将大有裨益，所以包名应该尽可能做到简短明了。按照惯例，包名应该使用小写的单个词汇，这样，就不需要使用下划线或者驼峰。`Err` 就是基于简短的考量，所有使用该包的用户都会使用这个名字。同时包也没有引入优先级一说。引入包的名字默认是它本身的名字，它无需在所有代码中都保持唯一性，如果遇到少数情况包名发生冲突，可以选择一个不一样的名字在本地使用。无论如何，通过导入时使用的文件名来判断使用的包很少会发生混淆。

另外一个惯例是我们常常使用源码目录名作为包名，比如 `src/encoding/base64` 目录下的包通常使用 `"encoding/base64"` 导入，它的包名则为 `base64`，而不是 `encoding_base64` 或者 `encodingBase64`。

被导入的包将会使用它的名字来引用包内容，因此包内可导出内容不会发生命名重叠。（不要使用 `import .` 这类语法，除非你想简化在被测包外运行测试的复杂性，否则不提倡这样做。）举例，`bufio` 包中带缓冲的读取类型叫 `Reader`，而不是 `BufReader`，因为用户使用时是这样用的 `bufio.Reader`，这无疑是一个简洁明了的名字。此外，由于导入的包总是命名为他们的包名，所以 `bufio.Reader` 就不会和 `io.Reader` 发生冲突。类似的，当我们构造实例 `ring.Ring`时，通常构造方法会命名为 `NewRing`，但是考虑到 `Ring` 是该包唯一可导出的，既然包名叫 `ring`，那我们就可以把方法命名为 `New`，这样，客户端就可以通过 `ring.New` 进行调用，使用包结构来帮助你更好的命名。

另外一个简短的例子是 `once.Do`。`once.Do` 很容易理解，如果我们把它改成 `once.DoOrWaitUntilDone`，似乎是画蛇添足。长命名和可读性并不是划等号的。一个有用的文档注释往往比冗长的命名更加有用。

### 获取器
`GO` 并不提供对获取器 (`getter`) 和 设置器（`setter`) 的支持。你应当自己提供该方法，通常这也是有价值的，但是将 `Get` 字眼放到方法中既不地道也没有必要。如果你有一个字段叫做 `owner`（它是小写，不可导出的），获取方法应当命名为 `Owner`（大写，可导出），而不是 `GetOwner`。大写字母即为可导出的这种规定为区分方法和字段提供了便利。设置器方法，如果需要的话，我们可以设置为 `SetOwner`。这样，两个方法看起来都很合理：
```golang
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

### 接口名
按照惯例，只有一个方法的接口应该在该方法后面加上 `-ex` 后缀或者采用类似的行为来构造一个代理名词：`Reader`，`Writer`，`Formatter`，`CloseNotifier` 等等。

诸如此类的命名有很多，遵循它们及它们所代表的函数名会让事情变得简单。`Read`，`Write`，`Close`，`Flush`，`String` 等名词具有公认的特征和意义，为了避免混淆，最好不要用它们命名你的方法除非它们拥有相同的含义。反过来说，如果你想实现一个方法和它们有着相同的含义，最好取相同的命名，比如命名你的字符串转换方法为 `String` 而不是 `ToString`。

### 驼峰命名
最后记住，`Go` 中使用驼峰命名 `MixedCaps` 或者 `mixedCaps`， 而不是下划线来对含多多个单词的名称命名。
