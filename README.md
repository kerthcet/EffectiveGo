# EffectiveGo![](https://visitor-badge.glitch.me/badge?page_id=kerthcet.EffectiveGo)

## 目录表
- [介绍](#介绍)
  - [示例](#示例)
- [格式化](#格式化)
- [注释](#注释)
- [命名](#命名)
- [分号](#分号)
- [控制结构](#控制结构)
- [方法](#方法)

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

## 分号
和 C 语言一样，Go 正式语法使用分号来结束语句，但是和 C 不同的是，这些分号不出现在源码中。取而代之的是词法分析程序会使用一条简单的规则在代码扫描的时候自动加上分号，源码则不需要使用分号。

这条规则就是如果换行符前最后一个符号为标识符（包括 `int` 和 `int64`）、基本字符如数字或者字符串常量，或者属于一下字符
```golang
break continue fallthrough return ++ -- ) }
```
那么词法分析器就会自动在符号后面加上分号。一句话就是“如何换行符在一个可以结束声明的符号后面，那么就添加分号”。

分号也可以在一个闭合的大括号之前省略掉，比如下面的声明语句就不需要分号。
```golang
go func() { for { dst <- <-src } }()
```
纯正的 Go 程序只有在像 `for` 循环语句中使用，用来分割初始化，条件判断和后续元素。如果你在一行中写多个语句，你也需要使用分号分割它们。

分号插入规则中有一点需要注意，就是你不能把表示控制结构（`if, for, switch, select`）开始的大括号放到下一行去，如果你这样做了，大括号之前就会被插入一个分号，引发不必要的结果。我们应该这样写
```golang
if i < f() {
    g()
}
```
而不是这样
```golang
if i < f()  // wrong!
{           // wrong!
    g()
}
```

## 控制结构
`Go` 的控制结构和 `C` 很像但也有一些独到之处。`Go` 没有 `do` 和 `while` 控制循环，只有一个简单通用的 `for`；`switch` 结构更加灵活；`if` 和 `switch` 可以和 `for` 循环一样接受一个可选的初始化申明；`break` 和 `continue` 语句用于标记什么时候跳出或者继续循环；另外，`Go` 语言引入了一些新的控制结构，包括类型控制器 `switch`，多路通信选择器 `select`。另外，语法也有一些不同：不使用圆括号，主体必须使用大括号包裹。

### If
`Go` 中一个简单的 `if` 语句如下：
```golang
if x > 0 {
    return y
}
```

强制要求使用的大括号使简单的 `if` 语句分成了多行。尤其是当主体拥有控制语句比如 `return` 和 `break` 时，这种编码风格的好处一看便知。

既然 `if` 和 `switch` 语句接受初始化声明，我们常常通过这种方法初始化本地变量：
```golang
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

在 `Go` 类库中，如果发现当一个 `if` 语句无法执行到下一步，比如以 `break`，`continue`，`goto` 或者 `return` 结尾的时候，`else` 语句可以忽略。
```golang
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```

下面是一个使用代码防范一系列错误的常见情形。如果控制流正常，则代码成功运行，避免抛出错误。由于错误一般都会以 `return` 结束，所以后面的代码也就无需使用 `else` 声明了。
```golang
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}
codeUsing(f, d)
```

### 重声明和重赋值
题外话：上一节最后一个示例解释了短声明 `:=` 是如何工作的，调用 `os.Open` 的声明语句为：
```golang
f, err := os.Open(name)
```

该语句声明了两个变量，`f` 和 `err`。后面又调用了 `f.Stat` 语句：
```golang
d, err := f.Stat()
```

看起来我们好像声明了 `d` 和 `err`，但是请注意，`err` 同时出现在了两个声明语句中。这种重复声明是合法的：`err` 由第一个声明语句声明，在第二个语句中只是被重新赋值了。这意味着 `f.Stat` 使用了已经存在的 `err` 变量，只是对它进行了重新赋值。

同时满足下列条件时，已被声明的变量v仍可以被 `:=` 声明：

* 本次声明与已声明的变量v处于同一作用域（若变量v已在外层作用域中声明，本次声明将会创建一个新的变量 §）
* 初始化声明时赋值给v的值应该与v类型相同
* 本次声明应该至少有一个变量是新声明的

这个不同寻常的特性非常实用，这让我们很容易的去使用 `err` 变量。例如，在一个很长的 `if-else` 语句中，你会经常看到它。

`§` 在这里根本就没有用，因为在 `Go` 语言中，方法参数，返回值和方法主体的作用域是一致的，尽管他们看起来在主体大括号之外。

### For
`Go` 的 `for` 循环和 `C` 语言相似但又不尽相同。它整合了 `for` 和 `while` ，但又没有 `do-while` 这种用法。它一共有3种形式，只有一种有分号。
```golang
// 像 C 语言的 for
for init; condition; post { }

// 像 C 语言的 while
for condition { }

// 像 C 语言的 for(;;)
for { }
```

短声明语句让我们方便的在循环中声明索引变量：
```golang
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```

如果你想遍历一个数组，切片，字符串或者字典，亦或者从通道中读取数据，`range`  语法可以帮助你：
```golang
for key, value := range oldMap {
    newMap[key] = value
}
```

如果你仅需要 `range` 中的第一项（键或者索引），把第二项去掉：
```golang
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```

如果你仅需要 `range` 中的第二项（值），使用空白标识符，即使用下划线来丢弃第一项：
```golang
sum := 0
for _, value := range array {
    sum += value
}
```

空白标识符有很多用途，我们会在后面的段落中讲到。

对字符串来说，`range` 的用途更多，他可以解析 `UTF-8` 将单个 `Unicode` 编码拆分出来。错误的编码将占用一个字节，并以符文 U+FFFD 来代替。（内建类型 `rune` 是 `Go` 中对单个 `Unicode` 码的名称术语。详情见[语言规范](https://go.dev/ref/spec#Rune_literals)）。以下循环语句：
```golang
for pos, char := range "日本\x80語" { // \x80 is an illegal UTF-8 encoding
    fmt.Printf("character %#U starts at byte position %d\n", char, pos)
}
```

将会打印：
```
character U+65E5 '日' starts at byte position 0
character U+672C '本' starts at byte position 3
character U+FFFD '�' starts at byte position 6
character U+8A9E '語' starts at byte position 7
```

最后，`Go` 没有逗号操作符，`++` 和 `--` 是声明语句而非表达式，因此如果你想要在 `for` 中声明多个变量，你可以使用平行赋值的方法（但是 `++` 和 `--` 就不能使用了）：
```golang
// 反转 a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

### Switch
`Go` 的 `switch` 语句比 `C` 语言更通用。其表达式无需为常量甚至整数，case 语句会自上而下逐一进行求值直到匹配为止。若 `switch` 后面不加表达式，则它将匹配 `true`。所以将一个链状的 `if-else-if-else` 语句用 `switch` 语句来替换是可行也是更符合 `Go` 语言代码风格的。
```golang
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

`Switch` 并不会自动下溯，但 `case` 可通过逗号分隔来列举相同的处理条件。
```golang
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

尽管 `Go` 中的语法同其他类 `C` 语言的语法不尽相同，但是在 `Go` 中 `break` 语句可以使 `switch` 语句提前终止。有时候 `switch` 语句被包裹在一个循环体中，我们想要跳出整个循环，在 `Go` 中可以通过在循环体内放置一个标签，然后使用 `break` 语句直接跳到标签处。下面是关于它们如何使用的示例：
```golang
Loop:
	for n := 0; n < len(src); n += size {
		switch {
		case src[n] < sizeOne:
			if validateOnly {
				break
			}
			size = 1
			update(src[n])

		case src[n] < sizeTwo:
			if n+1 >= len(src) {
				err = errShortInput
				break Loop
			}
			if validateOnly {
				break
			}
			size = 2
			update(src[n] + src[n+1]<<shift)
		}
	}
```

当前， `continue` 语句也接受一个可选标签，但是只能在循环内部使用。
我们以一个代码程序结束该章节，这段代码展示了使用两个 `switch` 语句对字节切片进行比较：
```golang
// Compare 按字典顺序比较两个字节切片并返回一个整数。
// 若 a == b，则结果为零；若 a < b；则结果为 -1；若 a > b，则结果为 +1。
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) > len(b):
        return 1
    case len(a) < len(b):
        return -1
    }
    return 0
}
```

### Type switch
`switch` 语句同样可以用来判断一个接口变量的动态类型。**type switch** 将 `type` 关键字放到圆括号中进行类型断言。如果表达式中声明了变量，该变量就会被赋值对应的类型。在 `case` 语句中复用名称是惯用的手法，实际上它会为每一个 `case` 声明一个同名的新变量。
```golang
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T\n", t)     // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t has type *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t has type *int
}
```

## 方法
### 多返回值
`Go` 语言不同于其他语言的特性之一就是函数和方法可以返回多个值。这种形式可以改善在 `C` 语言中一些笨拙的习惯：返回 `in-band` error 比如用 -1 表示 EOF，或者通过传入的参数地址修改对象。

在 `C` 语言中，写入错误通常使用负数的错误码表示，但是错误码可能隐藏在某个角落。而在 `Go` 语言中，`Write` 方法可以同时返回一个计数和一个错误：“是的，您写入了一部分字节但是没有全部写入，因为您的设备已满”。这个 `os` 包的 `Write` 方法签名如下：
```golang
func (file *File) Write(b []byte) (n int, err error)
```

正如文档所述，当 `n != len(b)`，该方法返回写入的字节数和一个非空错误。这也是常见的用法，更多的示例可以参考错误处理这一章节。

我们也可以采用一种类似的方法避免返回一个指针值来指向参数地址。下面是一个在字节切片中根据特定位置获取值的一个简单的方法，结果返回对应的值和下一个位置：
```golang
func nextInt(b []byte, i int) (int, int) {
    for ; i < len(b) && !isDigit(b[i]); i++ {
    }
    x := 0
    for ; i < len(b) && isDigit(b[i]); i++ {
        x = x*10 + int(b[i]) - '0'
    }
    return x, i
}
```

你可以使用该方法扫描切片 b 中的数字：
```golang
    for i := 0; i < len(b); {
        x, i = nextInt(b, i)
        fmt.Println(x)
    }
```

### 可命名的结果参数
`Go` 方法的返回结果参数可以被命名并且可以像普通参数一样使用，就像方法入参一样。一旦使用命名参数，当方法开始执行的时候，他们会被初始化为对应类型的零值；如果该方法执行了不带实参的 `return` 语句，那么结果参数的当前值会被返回。

这个命名不是强制的，但是会让代码显得简短清晰：它们就是文档本身。如果我们命名了 `nextInt` 返回的结果，那么我们就大概理解返回的 `int` 值代表什么意思了。
```golang
func nextInt(b []byte, pos int) (value, nextPos int) {
}
```

因为被命名的返回值已经初始化过并且绑定到无参数的 `return` 上，整个代码显得简单而又明了。下面的 `io.ReadFull` 很好的展示了这种用法：
```golang
func ReadFull(r Reader, buf []byte) (n int, err error) {
    for len(buf) > 0 && err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:]
    }
    return
}
```

### Defer 语句
`Go` 的 `defer` 语句用于预定义方法调用（即延迟执行方法），它会在方法返回前即刻调用。这是一个不太常见的但是很有效的方式，尤其是在处理无论方法以何种方式返回都需要进行资源释放等这类场景。一些很典型的示例比如解锁互斥锁或者关闭文件。
```golang
// Contents 方法会以字符串形式返回文件内容
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // 当我们的方法结束后，f.Close 将会执行

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...)
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // 如果我们在这里返回，文件 f 将会关闭.
        }
    }
    return string(result), nil // 如果我们在这里返回，文件 f 将会关闭.
}
```

延迟执行方法比如 `Close` 有两点优势。第一，保证我们绝不会忘记关闭文件，我们常常会在后期为该方法添加新的返回路径时忘了这件事。第二，关闭操作紧邻打开文件操作，这比把它放在方法末尾更显清晰。

延迟方法的参数（如果该方法是函数，同样包括它的接收者）会在 `defer` 语句执行时被求值，而不是被调用时才求值。除此之外我们也不用避免担心方法执行时变量的值会发生变更，这意味着单个延迟调用的代码行可以被用来延迟多个方法的执行。下面是一个仅仅用作示范的例子：
```golang
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```

延迟执行方法按照后先进出的顺序执行，因此上面的方法会返回 4 3 2 1 0。我们举一个更合理的例子，追踪程序中方法执行的过程。我们可以像这样写一组简单的追踪程序：
```golang
func trace(s string)   { fmt.Println("entering:", s) }
func untrace(s string) { fmt.Println("leaving:", s) }

// 我们可以这样使用
func a() {
    trace("a")
    defer untrace("a")
    // do something....
}
```

我们可以更进一步，利用延迟方法的参数在 `defer` 语句执行时就会被求值这个事实，来完善程序。该例子中，追踪程序会生成反追踪程序的参数：
```golang
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}

func main() {
    b()
}
```

打印结果：
```
entering: b
in b
entering: a
in a
leaving: a
leaving: b

```

对于习惯其他语言中按照块级别进行资源管理的程序员来说，`defer` 语句可能显得有些奇怪，但它最有趣且最强大的地方就在于它是基于函数而不是基于块的。在 `panic` 和 `recover` 节选中，我们会看到它另一种使用场景。
