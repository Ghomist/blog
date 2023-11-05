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

## 代码风格

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

### 组织结构

组织结构的层级如下：

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
