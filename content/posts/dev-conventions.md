+++
title = "项目风格公约"
date = 2023-11-05T15:26:36+08:00
draft = true
tags = ['conventions', 'c#']
series = []
+++

> 这篇公约比较偏个人向，声明的都是笔者个人的习惯（以及我个人所带项目的约定），可供参考，抛砖引玉
>
> 公约仍有很多不完善的地方，正处于逐步完善中

## 代码范例

该部分包含了 C# 代码的风格、范例和一些常用写法、小技巧

### Getter/Setter

一般情况下尽量不要暴露 public 成员变量，应使用访问器

```c#
private string _name;
public string Name { get; set; }
```

仅允许外部修改时可声明私有 setter

```c#
public string Name { get; private set; }
```

### Tuple

即元组，可以同时操作多个变量，是 C# 的一个语法糖，~~看起来像从 python 抄来的~~

#### 多重赋值

```c#
(int id, int code, string name) = (123, 456, "XYZ");
```

#### 变量交换

```c#
int a = 10;
int b = 20;
(a, b) = (b, a);
```

#### 封装

封装其实就是元组在代码中的实现方式 ~~（要不然为啥叫他叫语法糖捏）~~

```c#
// tuple type
(string, int) t = ("Hi", 10);

// with field name
(string name, int id) student = ("XYZ", 1234);

// with large number of fields
var t =
(1, 2, 3, 4, 5, 6, 7, 8, 9, 10,
11, 12, 13, 14, 15, 16, 17, 18,
19, 20, 21, 22, 23, 24, 25, 26);
Console.WriteLine(t.Item26);  // output: 26
```

### Readonly 与 Const

`readonly` 是声明运行时常量，旨在无法修改，`const` 是声明编译期常量，只接收字面量以及简单字面量的运算

`readonly` 关键字用于不同的位置时有不同的作用：

- [`readonly struct`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/struct#readonly-struct)：结构体类型不可变，所以只读结构体的成员均是隐式只读
- `readonly` 函数/ Getter ：声明为只读的成员无法对该结构内的其它成员进行修改，因为编译器会创建结构实例的副本，编译器可以根据 `readonly` 修饰符进行性能优化，详见[这里](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/struct#readonly-instance-members)

### Nullable Attribute

可为 null 的属性、变量，用法是声明变量时，在类型后面加上一个问号，在编写/使用 API 时要多加注意

```C#
public override bool Equals(object? obj)
{
    // ...
}
```

### Null 检查

#### 使用双问号标记

在 Setter 中设置属性不能为空（一般的空引用检查），此处若为空则抛出参数异常

```C#
private String _name;
public String Name
{
    readonly get => _name;
    set =>
        _name = value ?? throw ArgumentNullException(nameof(Name), "...");
}
```

#### 单行的空引用检查

使用丢弃变量 `_` 忽略赋值语句，即只进行 null 检查

```C#
_ = obj ?? throw ...
```

#### 在 Equals 方法中的空引用检查

使用三元运算符（ `is` 关键字判断时可赋值）

原始方法：

```C#
public override bool Equals(object? obj)
{
    if (obj is Point) // is 运算符只对引用类型生效
    {
        var otherPoint = (Point) obj;
        return this == otherPoint; // operation '==' has been overrode
    }
    else
    {
        return false;
    }
}
```

改进方法：

```C#
public override bool Equals(object? obj) =>
    (obj is Point otherPoint)
    ? this == otherPoint
    : false;
```

### 高级 Switch 语句

#### 一般形式

```C#
public bool SendCommand(int cmd) =>
    cmd switch
    {
        0 => true,
        1 => true,
        _ => false
    };
```

#### 配合 Tuple 匹配的高级 Switch

```C#
public int SendCommand(int cmd, int argv, bool type) =>
    (cmd, argv, type) switch
    {
        (1, 1, true) => 10,
        (1, 2, true) => 20,
        (2, 1, true) => 30,
        (1, 2, false) => 50,
        (3, _, _) => -100
    }
```

<!-- ### Linq -->

<!-- TODO -->

### Async 异步

C# 中的异步编程大多数情况下都是是由 `async/await` 机制构成，`await` 向后台线程池提交任务，等待新线程将其完成后继续，在此期间原线程不会阻塞

使用异步需要了解以下两点：

- `await` 只能在 `async` 函数当中使用
- `async` 函数会使编译后程序变大，不过影响甚小

> Every async method adds 100+ bytes, because every async method becomes a class.

以下是使用 `async/await` 的一些小技巧

#### await 和 task 同时存在

所有的 `async` 方法内部必须要有 `await`，否则它没有必要声明成一个 `async` 方法

对于任何一个异步函数（只要是返回 `Task` 的），都尽量使用 `await` 去等待

#### 同步执行异步函数

如果需要使用同步方法（强制等待）执行异步函数的话，务必不要使用 `Task.Wait()`，因为这样会阻塞当前线程的同时开启一个新的线程，可以使用 `Task.GetAwaiter().GetResult()` 替代

#### 线程优化

如果 await 回到的代码与UI线程无关（无需回到主线程），可以使用 `Task.ConfigureAwait(false)` 使另一个线程继续执行代码

```c#
// when need this thread no more
DoSthAsync().ConfigureAwait(false);
```

#### 减少 async 类

封装异步函数时可以不使用 `await`，并且直接 `return Task` 以去掉多余的 `async`，可以减少编译出来的异步类，但记得命名依旧要在末尾加上 `Async`，让人直觉上理解这是一个异步函数

```c#
// try not to do
async Task<void> PackMethodAsync() {
    await DoSthAsync();
}

// good
Task<void> PackMethodAsync() {
    return DoSthAsync();
}

// better
Task<void> PackMethodAsync() => DoSthAsync();
```

{{< alert >}}
如果需要 catch 异常，那么千万不要直接返回 Task，否则永远 catch 不到线程中的 Exception
{{< /alert >}}

#### 性能优化

如果方法的 hot path 没有出现 await 关键字（即该方法中存在分支，且 await 关键字仅存在于那些使用较少的分支内），可以将返回值改为 `ValueTask` 以避免 GC

> Hot path 是指一个方法中被大量使用的部分

#### 其它

避免使用 `async void` 函数，因为它几乎无法 catch 到线程抛出的异常，并且代码不同步，容易引发线程冲突

可以将 `async void` 函数替换为 `async Task`，并在使用时选择 `Task.SafeFireAndForget()` 函数

## 标识符命名

### 命名格式

该命名格式参考了 C# 指南中的[标识符名称](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/coding-style/identifier-names)部分，具体如下

1. 第一优先级（按声明）
   - `class`, `struct`, `delegate`：PascalCasing
   - `interface`：IPascalCasing
   - `const`：UPPER_SNAKE_CASE
2. 第二优先级（按访问权限）
   - `public`：PascalCasing
   - `private`, `internal`：_camelCasing
   - `private/internal static`：s_camelCasing
3. 第三优先级（按位置）
   - 枚举值：PascalCasing
   - 成员方法：PascalCasing
   - 局部变量：camelCasing

规则说明

- PascalCasing：单词之间不分割，所有单词开头字母大写，其余小写
- camelCasing：单词之间不分割，第一个单词全小写，其余单词开头字母大写
- snake_casing：单词之间以下划线分割，全部小写
- UPPER_SNAKE_CASING：单词之间以下划线分割，全部大写

### 描述性大于简洁性

名称应该具有自我描述性

```c#
// good
void SelectUserByName(string name);

// bad
void SelectUser(string name);
```

### 避免名称重复说明语境

例如类型名称、方法名称，他们能代表一定的语境，此时不需要在子层级内命名时带上语境名称

```c#
class Player
{
    // good
    void HandleMove();

    // bad
    void HandlePlayerMove();
}
```

### 数组、容器等应使用复数形式

```c#
int id;
int[] ids;
```

当同一种类型被用于多种不同容器、数组时应该在名称中有体现容器类型

```c#
Player[] PlayerArray;
List<Player> PlayerList;
```

### 合理使用反义词、技术术语

```c#
void StartAttack();
void StopAttack();

void EnableHealing();
void DisableHealing();

void Begin();
void End();

void Enter();
void Exit();

// for synchronization objects
void Acquire();
void Release();
```

### 类型参数/泛型

应当使用 `T`（ `Type` 的缩写）来指代，需多个泛型参数时从T开始以字母序向后依次取用

```c#
class Mob<T> { ... }

class Block<T, U, V> { ... }
```

当泛型参数有具体含义时，使用“ T + 具体含义”进行命名

```c#
// TAttack means 'attack type'
class Bullet<TAttack> { ... }
```

### 返回值为 bool 类型的方法/属性

依照可读性原则在前面加上 `is` `was` `will` `has` 等词

```c#
bool IsAvailable { get; set; }

bool HasWeapon(Weapon weapon) => { ... }

static bool WasUpgraded(Player player) => { ... }
```

但尽量避免使用 `not` 命名，对于带“否”意的调用可以统一使用`!`保持可读性和一致性

## 文件结构

### 文件命名

除了代码文件需使用 PascalCasing，其余文件全部使用 snake_casing

### 项目组织结构

项目文件组织结构的层级如下：

- 资源类别（大综分类）
- 细分
  - 按所属场景/关卡（分大类）
  - 按所属实体/节点（分小类）
- 资源本身

其中按照细分时可以分类多次（但建议最多3次，否则容易造成目录过深、结构过于复杂的问题），只要能保证结构清晰直观即可

> 判断结构是否清晰可以这样做：想找到一个资源文件能顺着文件夹结构直接找到，而不需要四处翻找

一个例子如下：（以下列表表示的是 res/assets 文件夹内的结构）

```txt
res
├── 3rd
├── icon
│   └── app.icon
├── img
│   ├── in_game
│   │   └── stone
│   │       ├── stone_2.png
│   │       └── stone.png
│   └── ui
│       ├── continue_button
│       │   ├── click.png
│       │   ├── hover.png
│       │   └── normal.png
│       └── start_button
│           ├── click.png
│           ├── hover.png
│           └── normal.png
├── sound
│   ├── bgm
│   │   ├── level_1.ogg
│   │   └── level_2.ogg
│   └── fx
│       ├── click.ogg
│       └── walk.ogg
└── src
    └── <place all stand-alone codes here>
```
