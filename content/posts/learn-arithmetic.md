+++
title = '基础算法学习路线'
date = 2023-11-21T22:02:14+08:00
draft = true
tags = ['算法', 'java']
series = []
+++

> 这篇文章主要是写给我的GF看的，类似于私人指导捏，所以免不了一些些的个人风格的观点~

这篇文章仅罗列对一些“偏基础且重要”的算法 ~~（且所谓的“阶段”分类带有强烈的个人观点）~~，另外附带一些 Java 打基础算法赛必学的类库

文章中的链接目前均来自于 [OI Wiki](https://oi-wiki.org/)，这是一个非常好的算法（以及竞赛相关知识）归纳总结站点，部分内容对初学者很友好，读过一遍后再做题可以对知识点有更深的理解

## 基础中的基础

首先编程的语法（不涉及 OOP 的部分）对你来说应该是没有问题的，这一点做不到的话可以先跟着别人的代码（抄题解，或者做一些小项目）进行练习

抄题解是用于巩固你自身的基础能力的，学一学大佬的写法能提高你的代码水平和算法水平，但**严禁抄题解刷AC，这种行为就毫无意义，只能骗骗自己，还容易引起别人反感**

做题的时候做不出其实是可以看题解的，但仅限于你经过大量的思考，穷尽了你目前对题目的理解（或者花费了相当多的时间），坚决不要只经过**少量**的思考就去翻题解

## 小试牛刀

这部分的算法都很基础，也比较简单，练习个几次基本上就能掌握了，最基本的**枚举**、**模拟**一般可以不用管。**枚举**就是把所有情况列出来，**模拟**就是题目怎么说你代码就怎么做，这都是比较符合直觉的、最暴力的算法了

可以从下面的这些算法入手

- [递归](https://oi-wiki.org/basic/divide-and-conquer/)：OI Wiki 解释的很不错，特别是这个[写递归的要点](https://oi-wiki.org/basic/divide-and-conquer/#%E5%86%99%E9%80%92%E5%BD%92%E7%9A%84%E8%A6%81%E7%82%B9)，可谓是非常精准了
- [贪心](https://oi-wiki.org/basic/greedy/)：只要理解它的思想即可，算法倒是其次
- [前缀和 & 差分](https://oi-wiki.org/basic/prefix-sum/)
- [二分](https://oi-wiki.org/basic/binary/)
- 非常简单的一些排序
  - [选择排序](https://oi-wiki.org/basic/selection-sort/)
  - [冒泡排序](https://oi-wiki.org/basic/bubble-sort/)
  - [插入排序](https://oi-wiki.org/basic/insertion-sort/)
  - [计数排序](https://oi-wiki.org/basic/counting-sort/)

一些基础的数据结构也要尽量熟练运用。不过他们对应的在 Java 中都已经实现，在保证自己了解其原理，并能够写出来一个简单的结构之后，就可以去了解类库中的用法了（毕竟真正打题一般不会手撸数据结构的吧）

- [链表](https://oi-wiki.org/ds/linked-list/)（`LinkedList`）
- [栈](https://oi-wiki.org/ds/stack/)（`Stack`/`LinkedList`）
- [队列](https://oi-wiki.org/ds/queue/)（`Queue`/`LinkedList`）
- [哈希表](https://oi-wiki.org/ds/hash/)（`HashMap`/`LinkedHashMap`/`HashSet`）

## 稍稍进阶

此阶段可以先了解一下下面两种特殊的储存结构

- [树](https://oi-wiki.org/graph/tree-basic/)
- [堆](https://oi-wiki.org/ds/heap/)，用数组和下标索引的方式表示一棵树有时也可以称作“堆式储存”

非常经典的两大暴力搜索算法，一定要会

- [深度优先搜索（DFS）](https://oi-wiki.org/search/dfs/)及图论中的[DFS遍历算法](https://oi-wiki.org/graph/dfs/)
- [广度优先搜索（BFS）](https://oi-wiki.org/graph/bfs/)

几个常见的稍微难一点的排序算法

- [快速排序](https://oi-wiki.org/basic/quick-sort/)
- [归并排序](https://oi-wiki.org/basic/merge-sort/)

常见的数据结构

- [二叉搜索树](https://oi-wiki.org/ds/bst/)（感兴趣可以继续延伸到 [AVL 树](https://oi-wiki.org/ds/avl/)以及[红黑树](https://oi-wiki.org/ds/rbtree/)等）
- [字典树](https://oi-wiki.org/string/trie/)
- [并查集](https://oi-wiki.org/ds/dsu/)（用得不多其实）
- [单调栈](https://oi-wiki.org/ds/monotonous-stack/)（需要背一点例题）
- [单调队列](https://oi-wiki.org/ds/monotonous-queue/)（需要背一点例题）

## 上一点难度

稍复杂一点的数据结构

- [树状数组](https://oi-wiki.org/ds/fenwick/)
- [线段树](https://oi-wiki.org/ds/seg/)
- 优先队列（`PriorityQueue`）

启发式搜索的代表算法之一

- [A*](https://oi-wiki.org/search/astar/)（念作 A-star）

还有别忘了，经典必考常考各种地方都能见到的：[动态规划](https://oi-wiki.org/dp/)，简称 DP（Dynamic Programming）

DP 有很多小分类，但其实其核心就是**状态转移**这一个东西，而状态转移通常是由第 n 个状态转移至第 n-1 个状态，所以最符合直觉的写法是[记忆化搜索](https://oi-wiki.org/dp/memo/)，其本质是 DFS 加上了记忆，从而可以减掉已经算过的部分，用到的写法是递归求解，倒序求解，所以这种方式有递归栈的限制，也会有答案记录、递归本身带来的开销

还有一种 DP 就是一般意义上的 DP，是纯粹的状态转移方程的体现。状态直接在数组中进行推算，最终推算至题目所需的结果，免除了递归栈，直接顺序推导结果

其实 DP 的核心就是找到状态转移方程，一旦找到，剩下的事情就和套模板一样简单（咳咳这也意味着你得写得够多才能熟练的把模板写出来），而通常情况下的一堆 DP 的分类，也不过是为了快速的判断类型从而迅速找到状态转移方程，所以可以了解，但不要过度依赖，下面只给出最基本的 DP 类型

- [背包 DP](https://oi-wiki.org/dp/knapsack/)（很重要，从背包问题出发你就能自己动手去推状态转移了）
- [区间 DP](https://oi-wiki.org/dp/interval/)（不过是选择从一个变成了一堆而已）
- [状压 DP](https://oi-wiki.org/dp/state/)（不过是压缩了状态而已）

## Java 选手特供

### 快读快写

Java 向来都是 IO 很慢的，甚至有时候只用 `Scanner` 刚读完样例就快爆时了，所以得用一点魔法来读那些比较大的数据样例

快读快写基于 `StreamTokenizer` 和 `PrintWriter`，输入量大就用快读，抛弃 `Scanner`，输出量大就用快写，抛弃 `System.out.print()`

下面的例子建议直接背下来，或者理解一下原理，其实不算麻烦

```java
// import java.io.*;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;

public class IO {
    private static PrintWriter pw = new PrintWriter(
            new OutputStreamWriter(System.out));

    private static StreamTokenizer st =
            new StreamTokenizer(
                new BufferedReader(
                    new InputStreamReader(System.in)));

    static int nextInt() throws IOException {
        st.nextToken();
        return (int) st.nval;
    }

    static String next() throws IOException {
        st.nextToken();
        return st.sval;
    }

    static void println(Object obj) {
        pw.println(obj);
    }

    static void flush() {
        pw.flush();
    }
}
```

### 数组处理

Java 内置了许多方便的库可以调用

填充数组（以及二维数组）

```java
int[] l = new int[20];
Arrays.fill(l, 1);

int[][] ll = new int[20][20];
for (int[] l : ll) Arrays.fill(l, 1);
```

数组排序（以及自定义排序）

```java
int[] l = ...

// 默认由小到大排序
Arrays.sort(l);

// 自定义比较器（这里实现效果是逆序，也就是从大到小排序）
Arrays.sort(l, (a, b) -> Integer.compare(b, a));
```

容器类排序

```java
List<Integer> l = ...

// 默认由小到大排序
Collections.sort(l);

// 自定义比较器（这里实现效果是逆序，也就是从大到小排序）
Collections.sort(l, (a, b) -> Integer.compare(b, a));

// 也可以直接调用列表的成员函数（注意这个函数必须传入比较器，否则会报错）
l.sort((a, b) -> Integer.compare(b, a));
```

### 优先队列
