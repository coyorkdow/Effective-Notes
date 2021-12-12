# 条款3: 理解decltype

`decltype`有别于模版推导和`auto`推导，在一般的视角来看，`decltype(e)`总是能准确给出表达式`e`的型别，但实际上`decltype`的规则要复杂得多。

1. 如果参数是一个标识符（通常是变量名或函数名）或者类成员表达式，则`decltype`恰好产生被这个标识符命名的对应的类型。
2. 如果参数是其它型别为`T`的表达式，则
	* 如果表达式为左值(lvalue)，则`decltype`产生左值引用`T&`。
	* 如果表达式为亡值(xvalue)，则`decltype`产生右值引用`T&&`。
	* 如果表达式为纯右值(prvalue)，则`decltype`产生`T`。

关于左值，亡值，纯右值的定义和区分，详细可以了解C++的值类别，在这里只做简单介绍。一般来说一个可以获取地址且不是亡值的表达式是lvalue，一个没有地址的表达式是纯右值，而一个可以获取地址，但是被作为“临时使用”的表达式（如`std::move(e)`）亡值。

上述这两条规则可能看起来十分晦涩，但是配合以下例子就容易理解了。

```c++
int x;
decltype(x); // 应用规则1，产生int
decltype((x)); // 应用规则2，产生int&
```

区别在于`(x)`已经不是一个标识符了，因此根据规则2，`(x)`是一个左值，所以产生了`int&`

另一个例子来源于cppreference，它阐述了规则1的第二种情况，即类成员的情况。

```c++
struct A { double x; };
const A* a;
 
decltype(a->x) y;       // y 的类型是 double (规则1)
decltype((a->x)) z = y; // z 的类型是 const double& (规则2，参数是左值)
```

`decltype`常常和`auto`配合使用，先看在`c++11`中常见的用法：返回类型后置。返回类型后置在`c++11`中用于解决模板函数的返回值不确定且无法仅通过模板形参来确定的问题。如以下代码，`authAndAccess(c, i)`被期望返回和`c[i]`完全一致的的结果，而`decltype(std::forward<Container>(c)[i]`产生的型别能满足这一要求。无论`c[i]`返回的是值还是引用，无论`c`是不是右值。

```c++
// c++11
template<typename Container, typename Index>
auto authAndAccess(Container&& c, Index i) -> decltype(std::forward<Container>(c)[i]) { 
  authenticateUser();
  return std::forward<Container>(c)[i];
}
```

在`c++14`中，`auto`的能力得到了增强，允许我们写出下面这样的代码。但是就如之前的条款所说的那样，`auto`推导对于可能要返回引用的情况捉襟见肘，事实上下面这样的代码永远不可能返回引用。

```c++
// c++14
template<typename Container, typename Index>
auto authAndAccess(Container&& c, Index i) { 
  authenticateUser();
  return std::forward<Container>(c)[i];
}
```

所幸`c++14`为我们提供了`decltype(auto)`，这两个关键词的组合看起来很令人费解，其实它的意思很简单：型别是推导出来的，而推导的时候用的是`decltype`的规则。以下的代码就和上面`c++11`的代码等价了，可见`decltype(auto)`用起来比返回类型后置再加上`decltype`要简洁多了。

```c++
// c++14
template<typename Container, typename Index>
decltype(auto) authAndAccess(Container&& c, Index i) { 
  authenticateUser();
  return std::forward<Container>(c)[i];
}
```
