+++
title = 'Tauri 单例'
date = 2023-12-15T17:32:27+08:00
draft = true
tags = ['rust', 'tauri']
series = []
+++

Tauri 是一个基于 Rust 和前端语言的 UI 框架，用于构建跨平台的桌面应用

我在写 App 全局状态的时候遇到了不小的问题，在这里记录一下

## 需求描述

假设现在需要做一个 Todo List App

很容易想到的一种实现方式，也就是后端维护一个 `List` 或者 `HashMap`，前端只负责显示列表和交互

想法很简单，但我在读完 Tauri 教程之后却没有一个好的实现方法

~~（事实证明是读的不够认真咳咳）~~

Tauri 的前端调用后端指令的方式如下，后端需要先声明一个 `command`，并且把它提交给 `App Builder` 进行注册

```rust
#[tauri::command]
pub fn greet(name: &str) {
    println!("Hello, {}! You've been greeted from Rust!", name);
}

fn main() {
    // build
    let app: App = tauri::Builder::default()
        // register here
        .invoke_handler(tauri::generate_handler![greet])
        .run(tauri::generate_context!())
        .expect("App failed");
}
```

前端可以直接通过函数调用

```ts
import { invoke } from "@tauri-apps/api/tauri";

async function greet() {
  greetMsg.value = await invoke("greet", { name: "Ghomist" });
}
```

看起来很简单，但是有一个小问题出现了：如果我需要用一个对象记录 Todo List，那么这个对象初始化的位置可能在 `main` 函数中，也可以在某个 `tauri command` 函数中，但初始化后的对象我应该存在哪里呢？于是出现了这个关于单例的问题

## 全局变量的限制

由于后端是 Rust，这个语言对单例的支持度本身很别扭（其实可以说是本来就不推荐），所以我在写全局状态的时候，最开始想到的，是类似于 C 语言一样，用一个全局变量储存（因为 Rust 本身和 C 语言也比较像）

Tauri 中实现全局变量的方式有两种，一个是 `const` 关键字，它只能储存字面常量，也就是编译期就会被处理掉的量，另一个是 `static` 变量，但是这个变量和运行时常量类似，不支持 `mut` 可变，并且初始化也必须知道所有的储存空间（也就是说 `HashMap` 这种对象无法直接创建 `static` 变量）

由于 Rust 中最安全的单例也就是直接在 `main` 函数中初始化对象，并且在程序运行的过程中保持它的生命周期，问题在于 Tauri 的 `command` 函数，它没有办法直接获取 `main` 函数中初始化的变量，而 Rust 也不允许以完全安全的方式储存一个全局的变量（但使用 `unsafe` 的确可以），所以 Tauri 肯定还提供了其它的方式进行全局状态管理

## Tauri 状态管理

Tauri 支持[全局状态管理](https://tauri.app/v1/guides/features/command/#accessing-managed-state)，这个功能的文档藏得不深，但是内容却很少，非常容易忽略掉

文档中提到的方法大致思路是：使用了元组结构体封装了一个 `String` 对象，然后在 `Builder` 处提交至 `manage` 函数，使用时直接自动注入该 `state`，就可以使用了

```rust
struct MyState(String);

#[tauri::command]
fn my_custom_command(state: tauri::State<MyState>) {
    assert_eq!(state.0 == "some state value", true);
}

fn main() {
    tauri::Builder::default()
        // register state here
        .manage(MyState("some state value".into()))
        .invoke_handler(tauri::generate_handler![my_custom_command])
        .run(tauri::generate_context!())
        .expect("error while running Tauri application");
}
```

但是这个方法还有一个问题，若在 command 处尝试修改 `state` 会报错，原因是传入的 `state` 不具有修改能力（详见这个 [issue](https://github.com/tauri-apps/tauri-docs/issues/831)）

这是 Tauri 的一个设定，暂时无法修改 `state`，目前最好的变通是用一个内部可变的对象（例如 `Mutex`）进行再一次的封装

```rust
struct StateWrapper(pub Mutex<String>);

fn main() {
    ...
}

#[tauri::command]
fn my_custom_command(state: tauri::State<StateWrapper>) {
    state.0.lock().unwrap() += "123";
}
```
