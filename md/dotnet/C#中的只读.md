# C#中的只读

## 常量 const

官方文档对常量解释是：常量是不可变的值，在编译时是已知的，在程序的生命周期内不会改变。

由以上定义可以得出：

- 因为常量的值不会发生改变，所以总是被视为类型定义的一部分，所以常量被定义为`static`，直接创建在元数据中的。
- 在编译时，编译器在定义常量的程序集的元数据中查找该值，直接嵌入到`IL`代码中，所以运行时不需要为其分配内存，无法通过引用传递，这些限制导致其无法跨程序集支持，改变常量的内容需要重新编译。
- 编译时确认就意味着编译器能够识别的基元类型，所以仅 C# [内置类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/built-in-types)（不包括 [System.Object](https://learn.microsoft.com/zh-cn/dotnet/api/system.object)）。

```C#
const string constStr = "const"; 
.field private static literal string constStr = "const"

private string str = constStr + "123";
IL_000b: ldarg.0      // this
IL_000c: ldstr        "const123"  //没有引用传递，直接编译在IL代码中
IL_0011: stfld        string PrimeService.CodeComplete.ConstantsTest::str
  
string str = "123";
string str2 = str + "123";

.locals init (
	[0] string str,
  [1] string str2
)
IL_0001: ldstr        "123"
IL_0006: stloc.0      // str

IL_0007: ldloc.0      // str
IL_0008: ldstr        "123"
IL_000d: call         string [System.Runtime]System.String::Concat(string, string) //调用Concat方法，引用传递两个str
IL_0012: stloc.1      // str2
```

此处修正两本书中的内容

- 《Effective C#》中称`readonly`为常量，但是`readonly`不满足官方文档对常量的定义
- 《CLR via C#》p141对static只能用于引用类型的说法已经过时。

### const

使用 `const` 关键字来声明某个常量字段或常量局部变量。 常量字段和常量局部变量不是变量并且不能修改。 常量可以为数字、布尔值、字符串或 null 引用。引用类型的常数，可能的值只能是 `string` 和 null 引用。

## readonly

`readonly`是运行期，所以使用的类型不受限制。

- 只读字段只能在声明时或在所属类型的构造函数中赋值
  - 由于引用类型包含对其数据的引用，因此属于 `readonly` 引用类型的字段必须始终引用同一对象。 该对象是可变的。`readonly` 修饰符可防止字段替换为引用类型的其他实例。 但是，修饰符不会阻止通过只读字段修改字段的实例数据。建议不要设置字段为可`set`。
- 在 `readonly struct` 类型定义中，`readonly` 指示结构类型是不可变的。
- 在结构类型内的实例成员声明中，`readonly` 指示实例成员不修改结构的状态。
- 在 [`ref readonly` 方法返回](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/readonly#ref-readonly-return-example)中，`readonly` 修饰符指示该方法返回一个引用，且不允许向该引用写入内容。

> 使用建议：
>
> const用来声明那些必须在编译期就确定的值，例如attribute的参数、switch case、enmu的定义等，或者是不会随着版本变化而改变的的值。const所带来的性能优化很小，除此之外应该考虑更加灵活的readonly。

## init

在`C#9`中新引入的关键字。`init` 关键字在属性或索引器中定义访问器方法。 init-only 资源库仅在对象构造期间为属性或索引器元素赋值。 这会强制实施不可变性，因此，一旦初始化对象，将无法再更改。

## private set

