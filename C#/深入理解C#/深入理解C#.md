

## 第一章 大浪淘沙

# 第二章 C#2

## 2.1 泛型

### 类型形参和类型实参

**形参**（parameter）和**实参**（argument）的概念，比C#泛型概念出现得还要早，其他一些语言使用形参和实参已有数十年之久。声明函数时用于描述函数输入数据的参数称为形参，函数调用时实际传递给函数的参数称为实参

实参的值相当于方法形参的初始值，而泛型涉及两个参数概念：**类型形参**（type parameter）和**类型实参**（type argument），相当于把普通形参和实参的思想用在了表示类型信息上。

在声明泛型类或者泛型方法时，需要把类型形参写在类名或者方法名称之后，并用尖括号`<>`包围。之后在声明体中，就可以像普通类型一样使用该类型形参了（只不过此时还不知道具体类型）。

```
//T为形参
public class List<T>
{

}
List<string> list =new List<string>();//类型实参
```

### 泛型类型和泛型方法的度

泛型**度**（arity）是泛型声明中类型形参的数量。

```
public void Method() {} <------ 非泛型方法（泛型度为0）
public void Method<T>() {} <------ 泛型度为1的方法
public void Method<T1, T2>() {} <------ 泛型度为2的方法
```

### 泛型的适用范围

并非所有类型或者类型成员都适用泛型。对于类型，这很好区分，因为可供声明的类型比较有限：枚举型不能声明为泛型，而类、结构体、接口以及委托这些可以声明为泛型类型。

**只需记住一条原则：判断一个声明是否是泛型声明的唯一标准，是看它是否引入了新的类型形参**。

方法和类型可以是泛型，但以下类型成员不能是泛型：

- 字段；
- 属性；
- 索引器；
- 构造器；
- 事件；
- 终结器。

### 约束类型

在泛型类型或泛型方法中声明类型形参时，可以使用**类型约束**来限定哪些类型可以用作类型实参。假设需要一个用于格式化列表元素的方法，该方法可以确保采用特定culture而不是默认culture来格式化。

元素都要实现`IFormattable`接口”这一点则需要类型约束来保证，做法就是在函数声明的末尾添加`where`语句，参考如下代码：

```
static void PrintItems<T>(List<T> items) where T : IFormattable
```

- **引用类型约束**——`where T : class`。类型实参必须是一个引用类型。（`class`这个关键字容易引起误解，它表示任何引用类型，包括所有接口和委托。）

- **值类型约束**——`where T : struct`。类型实参必须是非可空值类型（结构体类型或枚举类型）。可空值类型（2.2节会讲到）不适用于本约束。

- **构造器约束**——`where T : new()`。类型实参必须是公共的无参构造器。该约束保证了可以通过`new T()`创建一个`T`类型的实例。

- 转换约束`where T : SomeType`这里的`SomeType`可以是类、接口或者其他类型形参：



  - `where T : Control`
  - `where T : IFormattable`
  - `where T1 : T2`

把类型形参用于类型约束本身：

```c#
public void Sort(List<T> items) where T : IComparable<T>
```

当一个声明中存在多个类型形参时，每个类型形参都可以有各自的类型约束，如下所示：

```
TResult Method<TArg, TResult>(TArg input) <------ 具有两个类型形参TArg和TResult的泛型方法
    where TArg : IComparable<TArg> <------ TArg必须实现IComparable<TArg>接口
    where TResult : class, new() <------ TResult必须是具有无参构造器的引用类型
```

### `default`运算符和`typeof`运算符

`default`运算符的功能比较简单：它是一元运算符，其操作数是类型名或类型形参，返回值是该类型的默认值。

`typeof`运算符的使用相对复杂一些。考虑以下几种常见情形：

- 不涉及泛型，例如`typeof(string)`；

- 涉及泛型，但是不涉及类型形参，例如`typeof(List<int>)`；

- 仅涉及类型形参，例如`typeof(T)`；

- `typeof`操作数中有泛型，而且泛型作为类型形参出现，例如`typeof(List<TItem>)`，它出现在声明了`TItem`类型形参的方法体内部；

  其中第一个场景最简单，而且用法从未变过。对于其他场景，需要仔细考虑，尤其最后一个还引入了新语法。`typeof`运算符的返回值是`Type`类型的值，而且`Type`类在经过扩展之后可以支持泛型。那么上述几种情况都各自返回什么值呢？需要考虑很多情形，比如下面这几种。

- 涉及泛型，但是操作数中并没有出现类型实参，例如`typeof(List<>)`。

- 如果在包含`List<T>`定义的程序集中获取它的类型，那么结果是`List<T>`，不包含任何具体的类型实参，这被称为**泛型类型定义**。
- 如果在`List<int>`对象上调用`GetType()`方法，那么得到的结果将包含`int`这个类型实参的信息。
- 假设有一个泛型类定义如下：如果要获取它基类的类型，得到的类型将包含一个具体的类型形参（`string`）和一个类型形参形式的类型实参（`T`）。

`typeof(List<int>)`，其返回值是`List<int>`的`Type`值，结果与调用`new List<int>().GetType()`相同

接下来讨论`typeof(T)`。该表达式返回的是调用代码中`T`类型实参的`Type`。它的返回值永远是一个**封闭的**、**已构造的类型**，技术规范中将其描述为一个真正不包含任何类型形参的类型。

## 2.2 可空值类型

可空值类型特性背后的核心要素是`Nullable<T>`结构体。

```c#
public struct Nullable<T> where T : struct <------ 泛型结构体，其类型约束为非空值类型
{
    private readonly T value;
    private readonly bool hasValue;

    public Nullable(T value) <------ 提供了值的构造器
    {
        this.value = value;
        this.hasValue = true;
    } 
    public bool HasValue { get { return hasValue; } } <------ 用于检查值是否存在的属性

    public T Value  (本行及以下10行) 访问值，如果值不存在则抛出异常
    {
        get
        {
            if (!hasValue)
            {
                throw new InvalidOperationException();
            }
            return value;
        }
    }
}
// 后面还有点 gethashcode tostring什么的
```

### 装箱行为

当非可空值类型被装箱时，返回结果的类型就是原始的装箱类型，例如：

```
int x = 5;
object o = x;
```

`o`是对“装箱`int`”对象的引用。在C#中，“装箱`int`”和`int`之间的区别通常是不可见的：如果执行`o.GetType()`，返回的`Type`值会和`typeof(int)`的结果相同。

```c#
Nullable<int> noValue = new Nullable<int>();
object noValueBoxed = noValue; <------ 值的装箱操作，HasValue为false
Console.WriteLine(noValueBoxed == null); <------ 打印结果：True。装箱操作的结果是null引用

Nullable<int> someValue = new Nullable<int>(5);
object someValueBoxed = someValue; <------ 值的装箱操作，HasValue为true
Console.WriteLine(someValueBoxed.GetType()); <------ 打印结果：System.Int32，装箱操作的结果是装箱后的int
```

这正是理想的装箱行为，不过它有一个比较奇怪的副作用：在`System.Object`中声明的`GetType()`方法为非虚方法（不能重写），对某个值类型调用`GetType()`方法时总会先触发一次装箱操作。该行为或多或少会影响效率，但是还不至于造成困扰。如果对可空值类型调用`GetType()`，要么会引发`NullReferenceException`，要么会返回对应的非可空值类型，

```c#
Nullable<int> noValue = new Nullable<int>();
// Console.WriteLine(noValue.GetType()); <------ 会抛出NullReferenceException

Nullable<int> someValue = new Nullable<int>(5);
Console.WriteLine(someValue.GetType()); <------ 打印结果：System.Int32。与调用typeof(int)得到的结果一致
```

### 语言层面支持

**`?`后缀**

**`null`字面量**

\# 1中`null`表达式永远代指一个`null`引用。到了C# 2，**`null`的含义扩展了：或者表示一个`null`引用，或者表示一个`HasValue`为`false`的可空类型的值。`null`值可用于赋值、函数实参以及比较等任何地方。有一点需要强调：当`null`用于可空值类型时，它表示`HasValue`为`false`的可空类型的值，而不是`null`引用。**`null`引用和可空值类型不容易辨明，例如以下两行代码是等价的：

```
int? x = new int?();

int? x = null;
```

**转换**

存在从`T`到`Nullable<T>`的隐式类型转换，以及从`Nullable<T>`到`T`的显式类型转换。

对于任意两个非可空的值类型`S`和`T`，如果存在从`S`到`T`的类型转换（例如从`int`转换到`decimal`），那么以下类型转换都是合法的：

- `Nullable<S>`到`Nullable<T>`的类型转换（显式转换或隐式转换，视`S`到`T`的转换类型而定）；
- `S`到`Nullable<T>`的类型转换（同上）；
- `Nullable<S>`到`T`的显式类型转换。

**提升运算符**

C#允许对以下运算符进行重载。

- 一元运算符：`+`、`++`、`-`、`--`、`!`、`~`、`true`、`false`。
- 二元运算符**6**：`+`、`-`、`*`、`/`、`%`、`&`、`|`、`^`、`<<`、`>>`。
- 等价运算符：`==`、`!=`。
- 关系运算符：`<`、`>`、`<=`、`>=`。

使用以上运算符重载非可空类型`T`时，`Nullable<T>`也会重载相同的运算符，不过操作数类型和返回值类型会和非可空类型有所区别，这些都被称为**提升运算符**。无论是预定义运算符（比如数值类型的加法运算符），还是用户自定义运算符（比如为`DateTime`类添加一个`TimeSpan`运算符）都适用，此外还需要遵循以下规则：

- `true`和`false`运算符不能被提升，但二者很少用，因此影响不大；
- 只有操作数是非可空值类型的运算符才能被提升；
- 对于一元运算符和二元运算符（等价运算符和关系运算符除外），原运算符的返回类型必须是非可空的值类型；
- 对于等价运算符和关系运算符，原运算符的返回类型必须是`bool`类型；
- 作用于`Nullable<bool>`的`&`和`|`运算符具有单独定义的行为，稍后介绍

**提升运算符的执行结果是C#特有的**

**`as`运算符与可空值类型**

**说明**　对可空类型使用`as`运算符，性能出奇地低。大部分情况下，这不算太大的问题（还是要比I/O操作效率高），但是依然比采用`is`运算符完成转换性能低。我在几乎所有framework和编译器的组合上都试过上述操作，慢得确乎无疑。

对于目标结果是`Nullable<T>`类型的表达式来说，`as`是很方便的运算符；而且C# 7对大部分可空值类型采用模式匹配（详见第12章），故使用`as`运算符是更优的解决方案。

**空合并运算符`??`**

`??`是一个二元运算符，`first ?? second`表达式的计算分为以下几个步骤：

(1) 计算`first`表达式；

(2) 若结果不为`null`，则整个表达式的结果等于`first`的计算结果；

(3) 若结果为空，则继续计算`second`表达式，整个表达式的结果为`second`的计算结果。

## 2.3 简化委托的创建

所谓**方法组**，就是一个或多个同名方法。可以说，C#程序员每天都在不知不觉中使用方法组，因为每调用一次方法就是对方法组的一次使用。

匿名方法的真正威力，要等它用作**闭包**（closure）时才能发挥出来。闭包能够访问其声明作用域内的所有变量，即使当委托执行时这些变量已经不可访问。

## 2.4 迭代器

**迭代器**是包含**迭代器块**的方法或者属性。迭代器块本质上是包含`yield return`或`yield break`语句的代码，只能用于以下返回类型的方法或属性：

- `IEnumerable`
- `IEnumerable<T>`（`T`可以是类型形参，也可以是普通类型）
- `IEnumerator`
- `IEnumerator<T>`（`T`可以是类型形参，也可以是普通类型）

根据迭代器的返回类型，每个迭代器都有一个**生成类型**（yield type）。如果返回类型是非泛型接口，那么生成类型是`object`；如果返回类型是泛型接口，那么生成类型是该泛型接口实参的类型，比如`IEnumerator<string>`的生成类型是`string`。

`yield return`语句用于生成返回序列的各个值，`yield break`语句用于终止返回序列。

### 延迟执行

迭代器是延迟执行的。**延迟执行**（也称**延迟计算**）属于lambda演算的一部分，于20世纪30年代被提出。其基本思想十分简单：只在需要获取计算结果时执行代码。

**`IEnumerable`是可用于迭代的序列，`IEnumerator`则像是序列的一个游标。多个`IEnumerator`可以遍历同一个`IEnumerable`，并且不会改变`IEnumerable`的状态，而`IEnumerator`本身就是多状态的：每次调用`MoveNext()`，当前游标都会向前移动一个元素。**

如果还不太清楚，可以把`IEnumerable`想象成一本书，把`IEnumerator`想象成书签。一本书可以同时有多个书签，一个书签的移动不会改变书和其他书签的状态，但是书签自身的状态（它在书中的位置）会改变。`IEnumerable.GetEnumerator()`方法如同一个启动过程，它请求序列来创建一个`IEnumerator`用于迭代，就像把一个书签插入到一本书的起始页。

```
static IEnumerable<int> CreateSimpleIterator()
{ 
	yield return 10;
    for (int i = 0; i < 3; i++)
    {
        yield return i;
    }
    yield return 20;
}
```

当`CreateSimpleIterator()`被调用时，方法体中的代码都没有执行。

如果在`yield return 10`这一行插入断点后开始调试，就会发现方法被调用之后根本不会触发断点。调用`GetEnumerator()`方法同样不会触发断点。只有`MoveNext()`被调用时方法才会真正开始执行。然后怎么样呢？

### 执行`yield`语句

当如下几种情形之一发生时，代码会终止执行：

- 抛出异常；
- 方法执行完毕；
- 遇到`yield break`语句；
- 执行到`yield return`语句，迭代器准备返回值。

第一次`MoveNext()`调用还比较好理解，之后呢？之后不可能从头开始迭代，否则这个函数就陷入无限返回10的死循环了。实际上，当`MoveNext()`返回时，当前方法就仿佛被暂停了。生成的代码会追踪当前的语句执行进度，还会记录一些相关状态信息，比如循环中局部变量`i`的值。当`MoveNext()`再次被调用，就会从之前的位置继续执行，这就是**延迟执行**名称的由来。

### 延迟执行的重要性

用迭代器来表示一个无限长的序列，仅此而已。调用方可以根据需要来决定迭代次数或者自由地使用这些值。

### 处理`finally`块

```
//一个用于记录执行进度的迭代器
static IEnumerable<string> Iterator()
{
    try
    {
        Console.WriteLine("Before first yield");
        yield return "first";
        Console.WriteLine("Between yields");
        yield return "second";
        Console.WriteLine("After second yield");
    }
    finally
    {
        Console.WriteLine("In finally block");
    }
}
```

在运行这段代码之前，先预测一下迭代该序列会输出什么结果。特别是当返回`first`时，会输出`In finally block`这句吗？有以下两种思考方式。

- 如果认为在执行到`yield return`语句时，执行就暂停了，逻辑上讲执行还停留在`try`块中，那么就不会执行到`finally`块。
- 如果认为当执行到`yield return`时，代码实际上返回到了`MoveNext()`调用，感觉应该已经退出了`try`块，那么就应该正常执行`finally`块的代码。

这里就不卖关子了，答案是第一个。这种暂停执行的机制更加有效并且符合我们的直观预期。如果`try`块中每执行一次`yield return`语句，就需要执行一次`finally`块，而且在执行完方法的其他代码之后，还要再执行一次`finally`块。这种行为怎么看都不太正常。

前面的迭代器都是将整个序列全部进行迭代，因此还不算难理解，可是如果要求迭代中途停止呢？如果从迭代器获取元素的代码块只调用一次`MoveNext()`呢（比如只获取序列第一个元素这样的需求）？这种情况会不会让迭代器一直在`try`块之中暂停，而永远都不会去执行`finally`块呢？

答案是不会。**如果完全手动编写调用`IEnumerator<T>`的方法，然后只调用一次`MoveNext()`方法，那么最终将不会执行`finally`块。如果采用`foreach`循环，在序列全部迭代完成之前退出循环，那么将执行`finally`块。**

重点关注最后一行结果：执行了`finally`块。当退出`foreach`循环时，`finally`块自动执行，这是因为`foreach`循环中隐含了一条`using`语句。

```
static void Main()
{
    IEnumerable<string> enumerable = Iterator();
    //返回循环访问集合的枚举数。
    using (IEnumerator<string> enumerator = enumerable.GetEnumerator())
    {
        while (enumerator.MoveNext())
        {
            string value = enumerator.Current;
            Console.WriteLine("Received value: {0}", value);
            if (value != null)
            {
                break;
            }
        }
    }
}
```

`using`语句是重点。它保证了不管采用何种方式离开循环，都会调用`IEnumerator<string>`的`Dispose`方法。在调用`Dispose`方法时，如果此时迭代器还暂停在`try`块中也没有关系，`Dispose`方法会负责最终调用`finally`块，很智能吧！

### 处理`finally`的重要性

虽然`finally`块的处理属于比较细枝末节的内容，但它对于迭代器的实用性而言意义重大。这意味着迭代器可以用于那些需要释放资源的方法，比如文件处理器，它还意味着相同目的的迭代器可以链接起来使用。第3章将谈到LINQ to Objects需要频繁使用序列，对于文件或者其他资源的操作来说，可靠的资源释放机制至关重要。

**这些都要求调用方释放迭代器**

在迭代完序列最后一个元素之前，如果不调用迭代器的`Dispose`方法，就会发生资源泄漏或者内存清理延迟，因此应当避免这种情况。

虽然非泛型的`IEnumerator`接口并非扩展自`IDisposable`接口，但`foreach`循环会负责检查运行时实现是否实现了`IDisposable`接口，然后根据需要调用`Dispose`方法。泛型版的`IEnumerator<T>`由于本身就是扩展自`IDisposable`接口，因此比泛型的要简单。

**如果是手动调用`MoveNext()`来进行迭代（肯定会有这样的需求），也需要手动调用`Dispose`方法。如果是迭代泛型的`IEnumerable<T>`，如前所示使用`using`语句即可；而如果要迭代非泛型序列，那就需要像编译器处理`foreach`那样自行检查接口了。**

### 迭代器实现机制概览

```
public static IEnumerable<int> GenerateIntegers(int count)
{
 	try
    {
        for (int i = 0; i < count; i++)
        {
            Console.WriteLine("Yielding {0}", i);
            yield return i;
            int doubled = i * 2;
            Console.WriteLine("Yielding {0}", doubled);
            yield return doubled;
        }
    }
    finally
    {
        Console.WriteLine("In finally block");
    }
}
```

虽然只是一个简单的方法，但其中隐含了以下5点精心设计：

- 一个参数；
- 一个需要在`yield return`语句之间保留的局部变量；
- 一个不需要在`yield return`语句之间保留的局部变量；
- 两条`yield return`语句；
- 一个`finally`块。

# 第三章 LINQ以及相关特性

## 3.1 自动实现的属性

通过**自动实现的属性**（简称**自动属性**），C# 3大大简化了这一环节。有了自动属性之后，将由编译器负责实现原先的访问器部分，于是前一段代码可以缩减为一行：

```
public string Name { get; set; }
```

## 3.2 隐式类型

### 类型术语

可以用多个术语来描述编程语言与其类型系统的交互方式。有些人用**弱类型**（weakly typed）和**强类型**（strongly typed）来区分，不过我个人并不倾向于这样的定义。这两个术语缺乏明确的定义，开发人员在理解上会产生分歧。人们对类型系统的另外两种描述更具共识，它们是**静态类型**和**动态类型**、**显式类型**和**隐式类型**。下面依次介绍

**静态类型和动态类型**

静态类型的语言是典型的面向编译的语言：所有表达式的类型都由编译器来决定，并由编译器来检查类型的使用是否合法。假设要调用对象中的方法，编译器可以通过类型信息来查找合适的方法。查找的依据包括：调用方法的表达式的类型、方法名称、实参的类型和个数。这种决定某个表达式“具体含义”（调用哪个方法、访问哪个字段，等等）的过程称为**绑定**。动态类型的语言把绝大部分甚至所有绑定操作放到了执行期。

> **说明**　C#中有些表达式在源码层面不具有类型信息，比如`null`，但是编译器总是可以根据表达式所在上下文推断出其类型，然后根据该类型来检查表达式的使用是否正确。

C#总体上属于静态类型语言（不过C# 4引入了动态绑定，第4章将详述）。尽管具体调用虚函数的哪个实现取决于执行期调用对象的类型，但是执行方法签名绑定的整个过程都发生在编译时。

**显式类型和隐式类型**

在显式类型语言中，源码会显式地给出所有相关的类型信息，包括局部变量、字段、方法参数或者返回类型。隐式类型语言则允许开发人员不给出具体的类型信息，而是通过其他机制（编译器或者执行期的其他方式）根据上下文推断出类型。

C#总体上属于显式类型，不过在C# 3之前，就已经出现了隐式类型的身影，例如2.1.4节提到的对泛型类型实参的类型推断机制。另外，隐式类型转换的出现（比如从`int`到`long`的转换），也削弱了C#的显式类型特征。

### 隐式类型的局部变量

隐式类型的局部变量指使用上下文关键字`var`声明的变量。该声明方式与使用类型关键字声明的变量不同。

```
var language = "C#";
```

**提示**　C# 3刚推出的时候，很多开发人员刻意规避使用`var`声明变量。他们认为使用`var`会减少很多编译时类型检查，或者导致执行期出现性能问题。其实完全多虑了，它只是用于推断局部变量的类型。隐式声明的变量与显式声明的变量的行为完全一致。

基于类型推断的执行过程，可以得出关于隐式类型局部变量的两条重要的使用规则：

- 变量在声明时就必须被初始化；

- 用于初始化变量的表达式必须已经具备某个类型。

  

  主要用于以下场景

- 变量为匿名类型，不能为其指定类型。3.4节会讨论匿名类型，它是与LINQ相关的一项特性。
- 变量类型名过长，并且根据其初始化表达式可以轻松推断出类型。
- 变量的精确类型并不重要，并且初始化表达式可以提供足够的信息来推断。

### 隐式类型的数组

```
var array = new[] { 1, 2, 3, 4, 5 };
var array = new[,] { { 1, 2, 3 }, { 4, 5, 6 } };
```

## 3.3   对象和集合的初始化

读者可能会好奇：这些特性对于LINQ有什么用呢？前面曾提过，几乎C# 3的所有特性都是为LINQ服务的，那么对象初始化器和集合初始化器的作用何在呢？答案就是：与LINQ相关的其他特性都要求代码具备单一表达式的表达能力。（例如在一个查询表达式中，对于一个给定的输入，`select`子句不支持通过多条语句生成结果。）

## 3.4　匿名类型

使用匿名类型，无须预先声明一个类型便能创建静态类型对象。听起来当前类型应该是在执行期动态创建的，但是实际过程要更微妙。

```
var player = new //(本行及以下4行) 创建一个匿名类型的对象，包含Name和Score两个属性
{
    Name = "Rajesh",
    Score = 3500
};

Console.WriteLine("Player name: {0}", player.Name); (本行及以下1行) 打印属性值
Console.WriteLine("Player score: {0}", player.Score);
```

**以上代码依然属于静态类型的范畴。**Visual Studio会为`player`变量自动设置`Name`和`Score`属性。如果要访问一个不存在的属性（比如`player.Points`），则编译器会报错。属性的类型是根据赋值的类型进行推断的：`player.Name`是`string`类型，`player.Score`是`int`类型。

### 编译器生成类型

当采用微软C#编译器时，匿名类型具备以下特点。

- 它是一个类（保证）。
- 其基类是`object`（保证）。
- 该类是密封的（不保证，虽然非密封的类并没有什么优势）。
- 属性是只读的（保证）。
- 构造器的参数名称与属性名称保持一致（不保证，有时对于反射有用）。
- 对于程序集是internal的（不保证，在处理动态类型时会比较棘手）。

- 该类会覆盖`GetHashCode()`和`Equals()`方法：两个匿名类型只有在所有属性都等价的情况下才等价。（可以正常处理`null`值。）只保证会覆盖这两个方法，但不保证散列值的计算方式。
- 覆盖并完善`ToString()`方法，用于呈现各属性名称及其对应值。这一点不保证，但对于问题诊断来说作用重大。
- 该类型为泛型类，其类型形参会应用于每一个属性。具有相同属性名称但属性类型不同的匿名类型，会使用相同的泛型类型，但拥有不同的类型实参。这一点不保证，不同编译器的实现方式不同。
- 如果两个匿名对象创建表达式使用相同的属性名称，具有相同的属性类型以及属性顺序，并且在同一个程序集中，那么这两个对象的类型相同。

### 匿名类型的局限性

匿名类型在需要实现数据的局部化表示时能够发挥作用。所谓**局部化**，就是指某个数据形态的使用范围限制在特定方法中。

在C# 7之前，如果需要在多个方法中使用同一个数据结构，一般需要声明自定义类或结构体。C# 7引入了元组（第11章会介绍），元组可作为候选解决方案，不过依然取决于数据封装程度的需求。

## 3.5　lambda表达式

```
Func<int, int, int> multiply = (本行及以下1行) 最长的版本
    (int x, int y) => { return x * y; };

Func<int, int, int> multiply = (int x, int y) => x * y; <------ 使用表达式主体

Func<int, int, int> multiply = (x, y) => x * y; <------ 推断参数类型
(有两个参数，因此不能省略圆括号)
```

### 捕获变量

```
class CapturedVariablesDemo
{
    private string instanceField = "instance field";

    public Action<string> CreateAction(string methodParameter)
    {
        string methodLocal = "method local";
        string uncaptured = "uncaptured local";

        Action<string> action = lambdaParameter =>
        {
            string lambdaLocal = "lambda local";
            Console.WriteLine("Instance field: {0}", instanceField);
            Console.WriteLine("Method parameter: {0}", methodParameter);
            Console.WriteLine("Method local: {0}", methodLocal);
            Console.WriteLine("Lambda parameter: {0}", lambdaParameter);
            Console.WriteLine("Lambda local: {0}", lambdaLocal);
        };
        methodLocal = "modified method local";
        return action;
    }
}
// 其他代码

var demo = new CapturedVariablesDemo();
Action<string> action = demo.CreateAction("method argument");
action("lambda argument");
```

- `instanceField`是`CapturedVariablesDemo`类的一个实例字段，为lambda表达式所捕获；
- `methodParameter`是`CreateAction`方法的一个参数，为lambda表达式所捕获；
- `methodLocal`是`CreateAction`方法中的一个局部变量，为lambda表达式所捕获；
- `uncaptured`是`CreateAction`方法中的一个局部变量，因为没有被lambda表达式使用，所以不属于捕获变量；
- `lambdaParameter`是lambda表达式自己的参数，不属于捕获变量；
- `lambdaLocal`是lambda表达式内部的局部变量，不属于捕获变量。

需要重点关注的是，这些lambda表达式捕获的是这些变量本身，而不是委托创建时这些变量的值2。如果在委托定义后到委托调用前这一期间修改任何一个捕获变量，那么这些修改都会在输出结果中体现出来。同样，lambda表达式自己也能够修改这些捕获变量的值，那么编译器如何保证这些变量在委托调用时依然可用呢？



>- 如果没有捕获任何变量，那么编译器可以创建一个**静态方法**，不需要额外的上下文。
>- 如果仅捕获了实例字段，那么编译器可以创建一个**实例方法**。在这种情况下，捕获1个实例字段和捕获100个没有什么差别，只需一个`this`便可都可以访问到。
>- 如果有局部变量或参数被捕获，编译器会创建一个**私有的嵌套类**来保存上下文信息，然后在当前类中创建一个**实例方法**来容纳原lambda表达式的内容。原先包含lambda表达式的方法会被修改为使用嵌套类来访问捕获变量。



> **具体实现细节因编译器而异**

```
public Action<string> CreateAction(string methodParameter)
{
    string methodLocal = "method local";
    string uncaptured = "uncaptured local";

    Action<string> action = lambdaParameter =>
    {
        string lambdaLocal = "lambda local";
        Console.WriteLine("Instance field: {0}", instanceField);
        Console.WriteLine("Method parameter: {0}", methodParameter);
                Console.WriteLine("Method local: {0}", methodLocal);
        Console.WriteLine("Lambda parameter: {0}", lambdaParameter);
        Console.WriteLine("Lambda local: {0}", lambdaLocal);
    };
    methodLocal = "modified method local";
    return action;
}
```

实例代码中编译器会创建一个私有的嵌套类来保存额外的上下文信息，然后在该类中创建一个实例方法用于容纳lambda表达式的代码。

- 一个`CapturedVariablesDemo`类实例的引用，用于之后访问`instanceField`；
- 一个`string`变量来保存捕获的方法参数；
- 一个`string`变量来保存捕获的局部变量。

捕获变量的lambda表达式转译后的代码

```
private class LambdaContext <------ 生成类保存捕获变量
{
    public CapturedVariablesDemoImpl originalThis; (本行及以下2行) 捕获的变量
    public string methodParameter;
    public string methodLocal;

    public void Method(string lambdaParameter) <------ lambda表达式体变成一个实例方法
    {
        string lambdaLocal = "lambda local";
        Console.WriteLine("Instance field: {0}",
            originalThis.instanceField);
              Console.WriteLine("Method parameter: {0}", methodParameter);
        Console.WriteLine("Method local: {0}", methodLocal);
        Console.WriteLine("Lambda parameter: {0}", lambdaParameter);
        Console.WriteLine("Lambda local: {0}", lambdaLocal);
    }
}

public Action<string> CreateAction(string methodParameter)
{
    LambdaContext context = new LambdaContext(); (本行及以下7行) 生成类用于所有捕获的变量
    context.originalThis = this;
    context.methodParameter = methodParameter;
    context.methodLocal = "method local";
    string uncaptured = "uncaptured local";

    Action<string> action = context.Method;
    context.methodLocal = "modified method local";
    return action;
}
```

同样，如果委托自己修改了任何捕获的变量，那么每个委托的调用都会受到前一个调用的影响。再次强调：编译器捕获的是变量本身，而不是变量值的副本。

**局部变量的多次实例化**

```
static List<Action> CreateActions()
{
    List<Action> actions = new List<Action>();
    for (int i = 0; i < 5; i++)
    {
        string text = string.Format("message {0}", i); <------ 在循环内部声明局部变量
        actions.Add(() => Console.WriteLine(text)); <------ 在lambda表达式中捕获该变量
    }
    return actions;
}

// 其他代码

List<Action> actions = CreateActions();
foreach (Action action in actions)
{
    action();
}
```

编译器的做法是：每次初始化都创建一个不同的生成类型实例，因此代码清单3-8中的`CreateAction`方法会转译成如下形式。

> **代码清单3-9**　为每次初始化创建上下文实例

```
private class LambdaContext
{
    public string text;

    public void Method()
    {
        Console.WriteLine(text);
    }
}

static List<Action> CreateActions()
{
    List<Action> actions = new List<Action>();
    for (int i = 0; i < 5; i++)
    {
        LambdaContext context = new LambdaContext(); <------ 为每次循环都创建一个新的上下文
        context.text = string.Format("message {0}", i);
        actions.Add(context.Method); <------ 使用上下文创建一个action
    }
    return actions;
}
```

**多个作用域下的变量捕获**

```
static List<Action> CreateCountingActions()
{
    List<Action> actions = new List<Action>();
    int outerCounter = 0; <------ 两个委托捕获同一个变量
    for (int i = 0; i < 2; i++)
    {
        int innerCounter = 0; <------ 每次循环都创建一个新变量
        Action action = () =>
        {
            Console.WriteLine( (本行及以下4行) 计数器打印和自增
                "Outer: {0}; Inner: {1}",
                outerCounter, innerCounter);
            outerCounter++;
            innerCounter++;
        };
        actions.Add(action);
    }
    return actions;
}

// 其他代码

List<Action> actions = CreateCountingActions();
actions[0](); (本行及以下3行) 每个委托调用两次
actions[0]();
actions[1]();
actions[1]();
//执行结果如下
Outer: 0; Inner: 0
Outer: 1; Inner: 1
Outer: 2; Inner: 0
Outer: 3; Inner: 1
```

每个委托都需要各自的上下文，但是各自的上下文还需要指向一个公共的上下文。编译器是如何处理这种情况的呢？答案是创建两个私有嵌套类。

```
private class OuterContext (本行及以下3行) 外层作用域的上下文
{
    public int outerCounter;
}

private class InnerContext (本行及以下3行) 包含外层上下文引用的内层作用域上下文
{
    public OuterContext outerContext;
    public int innerCounter;

    public void Method() <------ 用于创建委托的方法
    {
        Console.WriteLine(
            "Outer: {0}; Inner: {1}",
            outerContext.outerCounter, innerCounter);
        outerContext.outerCounter++;
        innerCounter++;
    }
}

static List<Action> CreateCountingActions()
{
    List<Action> actions = new List<Action>();
    OuterContext outerContext = new OuterContext(); <------ 创建一个外层上下文
    outerContext.outerCounter = 0;
    for (int i = 0; i < 2; i++)
    {
        InnerContext innerContext = new InnerContext(); (本行及以下2行) 每次循环都创建一个内层上下文
        innerContext.outerContext = outerContext;
        innerContext.innerCounter = 0;
        Action action = innerContext.Method;
        actions.Add(action);
    }
    return actions;
}
```

### 表达式树

**表达式树**是将代码按照数据来表示的一种形式。这项特性是LINQ能够有效处理SQL数据库这类数据提供者的核心秘诀所在。

```
Expression<Func<int, int, int>> adder = (x, y) => x + y;
Console.WriteLine(adder);
```

首先看`adder`的类型：`Expression<Func<int, int, int>>`。把它拆解成两部分：`Expression<TDelegate>`和`Func<int, int, int>`。第2部分是第1部分的类型实参，它是一个代理类型，由两个`int`参数和一个`int`返回值构成。（返回值类型由最后一个参数指定，例如`Func<string, double, int>`的意思是接收`string`和`double`类型的参数，返回`int`类型值。）

**转换表达式树的局限性**

只有拥有表达式主体的lambda表达式才能转换成表达式树，这条规则最为重要。`(x, y) => x + y`符合该规则，但下面这句代码会编译报错：

```
Expression<Func<int, int, int>> adder = (x, y) => { return x + y; };
```

**将表达式树编译成委托**

`Expression<TDelegate>`有一个`Compile()`方法，该方法返回一个委托类型。

```
Expression<Func<int, int, int>> adder = (x, y) => x + y;
Func<int, int, int> executableAdder = adder.Compile(); <------ 将表达式树编译成委托
Console.WriteLine(executableAdder(2, 3)); <------ 正常调用委托
```

## 3.6　扩展方法

## 3.7　查询表达式

查询表达式引入了**范围变量**的概念。范围变量与普通变量不同，范围变量充当了查询语句中每条子句中的输入。

> 使用`let`子句引入新的范围变量

```
from word in words
let length = word.Length
where length > 4
orderby length
select string.Format("{0}: {1}", length, word.ToUpper());
```

> 使用隐形标识符对查询进行转译

```
words.Select(word => new { word, length = word.Length })
     .Where(tmp => tmp.length > 4)
     .OrderBy(tmp => tmp.length)
     .Select(tmp =>
         string.Format("{0}: {1}", tmp.length, tmp.word.ToUpper()));
```

这里的`tmp`不属于查询转译的一部分，语言规范中是用`*`符号表示的。

## 3.8　终极形态：LINQ

```
var products = from product in dbContext.Products
               where product.StockCount > 0
               orderby product.Price descending
               select new { product.Name, product.Price };
```

短短4行代码，应用了所有新特性。

- 匿名类型，包括投射初始化器（只选择`name`和`price`这两个属性）。
- 使用`var`声明的匿名类型，因为无法声明`products`变量的有效类型。
- 查询表达式。当然对于本例可以不使用查询表达式，但对于更复杂的情况，使用查询表达式能事半功倍。
- lambda表达式。lambda表达式在这里作为查询表达式转译之后的结果。
- 扩展方法。它使得转译后的查询可以通过`Queryable`类实现，因为`dbContext.Products`实现了`IQueryable<Product>`接口。
- 表达式树。它使得查询逻辑可以按照数据的方式传给LINQ提供器，然后转换成SQL语句并交由数据库执行。

# 第 4 章　C# 4：互操作性提升

## 4.1　动态类型

> 使用动态类型获取子串

```
dynamic text = "hello world"; <------ 使用动态类型声明变量
string world = text.Substring(6); <------ 调用Substring方法。没有问题
Console.WriteLine(world);

string broken = text.SUBSTR(6); <------ 调用SUBSTR。抛出异常
Console.WriteLine(broken);
```

在特定上下文中查找符号含义的过程称为**绑定**。动态类型是把绑定过程从编译时转移到了执行期。使用静态类型，编译器生成的IL代码中包含的是精准的方法签名的调用；而使用动态类型，编译器生成的IL代码的功能是执行绑定并执行绑定的结果。这一切都是由`dynamic`关键字触发的。

- **什么是动态类型**

在C#中使用`dynamic`，IL都会转换成带有`[Dynamic]`属性的`object`来声明。

(1) 非指针类型到`dynamic`类型存在隐式类型转换。

(2) `dynamic`类型的表达式到任意非指针类型存在隐式类型转换。

(3) 如果表达式中有`dynamic`类型的值，通常都是在执行期才完成绑定的。

(4) 大部分含有`dynamic`类型值的表达式，表达式编译时的类型也是`dynamic`。

- **在多个上下文中应用动态绑定**

执行期的绑定行为会尽可能与编译时的绑定行为一致，其绑定依据是动态值在执行期的类型。

**只有动态值才会被动态处理**

- **在动态绑定的上下文中，编译器做哪些检查**

如果在编译时就能获得一个方法调用的上下文，那么编译器可以检查能否找到该特定名称的方法。如果找不到能够在执行期调用的方法，依然会引发编译错误。该规则适用于以下场景：

- 目标不是动态值的实例方法和索引器；
- 静态方法；
- 构造器。

**有哪些有动态值参与的操作，但并非动态绑定**

绝大部分处理动态值的操作会涉及：类型绑定、查找合适方法、属性、转换或者运算符等，但还有一些情况，编译器不需要为其生成绑定代码。

- 给一个类型是`object`或者`dynamic`的变量赋值。因为不需要类型转换，所以编译器只需复制现有的引用。
- 传入方法的参数类型是`object`或者`dynamic`。和上一条类似，只不过是把变量换成参数。
- 使用`is`运算符判断某个值的类型。
- 使用`as`运算符转换某个值的类型。

**哪些操作有动态值参与但是依然是静态类型**

- `new SomeType(d)`表达式具备编译时类型`SomeType`，尽管该构造器在执行期才会被动态绑定。
- `d is SomeType`表达式具备编译时类型`bool`。
- `d as SomeType`表达式具备编译时类型`SomeType`。

### 超越反射的动态行为

**Json.NET的动态视图**

```
string json = @" (本行及以下7行) 硬编码的JSON数据
    {
      'name': 'Jon Skeet',
      'address': {
        'town': 'Reading',
        'country': 'UK'
      }
    }".Replace('\'', '"');

JObject obj1 = JObject.Parse(json); <------ 将JSON解析成JObject

Console.WriteLine(obj1["address"]["town"]); <------ 使用静态类型视图

dynamic obj2 = obj1; (本行及以下1行) 使用动态类型视图
Console.WriteLine(obj2.address.town);
```

**一个假想的数据库访问的例子**

```
dynamic database = new Database(connectionString);
var books = database.Books.SearchByAuthor("Holly Webb");
foreach (var book in books)
{
    Console.WriteLine(book.Title);
}
```

- `Database`类会对`Books`属性请求做出响应，它会向数据库schema发起对`Books`表的请求，并且返回某个`table`对象。
- 该`table`对象可以响应`SearchByAuthor`方法调用：它识别出该方法以`SearchBy`开头，然后在schema中查找一个名为`Author`的列，接着生成SQL语句，通过提供的参数查询该列，并且返回一个行对象的列表。
- 每个行对象都可以响应`Title`属性并且返回`Title`列的值。

**`ExpandoObject`：一个装有数据和方法的动态袋子**

NET Framework提供了一个名为`ExpandoObject`的类型，位于`System.Dynamic`命名空间下。该类型有两种工作模式，视是否将该类型当作动态值而定。

# 编写异步代码



`async`关键字可以放在返回类型前的任意位置。例如以下声明均合法：

```
public static async Task<int> FooAsync() { ... }
public async static Task<int> FooAsync() { ... }
async public Task<int> FooAsync() { ... }
public async virtual Task<int> FooAsync() { ... }
```

异步函数的返回值仅限于以下4个类型：

- `void`；
- `Task`；
- `Task<TResult>`（某些`TResult`本身也可以是类型形参）。
- 自定义task类型

**警告**　返回`void`类型的异步方法最好只用于事件订阅中。对于其他不需要返回特定值的情况，把异步方法的返回类型声明为`Task`更好。这样调用方才能await操作完成、发现操作失败，等等。

`async`方法的参数不能由`out`或者`ref`修饰。这是因为`out`和`ref`参数是用于与调用方交换信息的，有时`async`方法在控制流返回到调用方时，操作可能还未开始执行，因此引用参数可能尚未赋值。

## `await`表达式

使用`async`来声明方法旨在在方法内部使用`await`表达式。除此之外，`async`方法的其他部分与普通方法无异。

不过`await`所搭配的表达式也有条件限制，必须是**可等待**的，下面介绍这个所谓的**可等待模式**。

### 可等待模式

假设有一个返回类型为`T`的表达式需要使用`await`，编译器会执行以下检查步骤。

- `T`必须具备一个无参数的`GetAwaiter()`实例方法，或者存在`T`的扩展方法，该方法以类型`T`作为唯一参数。`GetAwaiter`方法的返回类型不能是`void`，其返回类型称为awaiter类型。
- awaiter类型必须实现`System.Runtime.INotifyCompletion`接口，该接口中只有一个方法：`void OnCompleted(Action)`。
- awaiter类型必须具有一个可读的实例属性`IsCompleted`，其类型为`bool`。
- awaiter类型必须具有一个非泛型、无参数的实例方法`GetResult`。
- 上述成员不必为public，但是这些成员需要能被调用`await`的async方法访问到。（因此存在这样的可能性：对于某个类型，在某些代码中可以使用`await`，但在其他代码中不可行，不过这种情况十分罕见。）

## 异步方法执行流程

描述异步行为时，最困难的应该是区分**方法返回**（返回到原调用方或者某个续延）和**方法完成**这两个概念。与多数方法不同，异步方法可以多次返回，当前没有任务即可返回

```
static void Main()
{
    Task task = DemoCompletedAsync(); <------ 调用async方法
    Console.WriteLine("Method returned");
    task.Wait(); <------ 阻塞，直到task完成
    Console.WriteLine("Task completed");
}

static async Task DemoCompletedAsync()
{
    Console.WriteLine("Before first await");
    await Task.FromResult(10); <------ await一个已经完成的task
    Console.WriteLine("Between awaits");
    await Task.Delay(1000); <------ await一个尚未完成的task
    Console.WriteLine("After second await");
}
/结果如下
Before first await
Between awaits
Method returned
After second await
Task completed
```

- `async`方法在await已完成的task时不返回，此时方法还是按照同步方式执行。执行结果的前两行显示，这两行输出结果之间没有其他输出内容。
- **当`async`方法`await`延迟task时，`async`方法会立即返回。因此`Main`方法的第3行输出结果是`Method returned`。`async`方法能够得知当前`await`的操作尚未完成，因此选择返回以防止阻塞。**
- `async`方法返回的task只有在方法完成后才完成，因此`Task completed`在`After second await`之后打印。

### 异常拆封

`Task`和`Task<TResult>`表示失败的方式如下。

- 当某个操作失败时，任务的`Status`变成`Faulted`（并且`IsFaulted`的值为`true`）。
- `Exception`属性返回一个`AggregateException`，它包含导致任务失败的所有（可能多个）异常。如果任务没有faulted，则该属性值为`null`）。

- 如果任务最后的状态为`Faulted`，`Wait()`方法会抛出一个`AggregateException`。
- `Task<TResult>`（也在等待完成）的`Result`属性可能抛出一个`AggregateException`。
