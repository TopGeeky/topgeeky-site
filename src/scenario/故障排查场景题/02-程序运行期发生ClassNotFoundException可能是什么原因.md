---
title: 02-程序运行期发生ClassNotFoundException可能是什么原因
description: 故障排查类问题主要考察候选人有没有接触过真实项目，在面对故障的时候的解决问题的思路是什么？如何准确且高效地定位和解决问题。
author: TopGeeky
icon: "circle-check"
---

## ClassNotFoundException出现的原因

`ClassNotFoundException`这个错误应该是Java程序员能够遇到比较常见的错误，比如**程序编译时找不到某个类的class文件**, 这个在开发时是常有的也很容易解决，还有就是在运行时候出现这个问题，这个就需要花点时间来寻找原因。

`ClassNotFoundException` **是一个** `checked exception`（受检异常），继承自 java.lang.Exception，属于 Java 的受检异常。

它通常在尝试通过类的全限定名（比如 Class.forName()、ClassLoader.loadClass()）动态加载类时，如果目标类无法找到，就会抛出这个异常。



主要特征：

​	•	**属于运行时动态加载异常**：只有在运行时尝试加载类时，才可能出现。

​	•	**必须显式处理或声明**：由于是受检异常，程序在编译时就要求开发者处理它（通过 try-catch 或 throws）。