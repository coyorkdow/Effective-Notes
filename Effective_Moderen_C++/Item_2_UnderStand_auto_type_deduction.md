# 条款2: 理解auto型别推导

绝大部分情况下，`auto`推导和模板推导是相同的。对于以下伪代码

```c++
template<typename T>
void f(ParamType param);
```

`auto`推导中，`auto`扮演了模板中`T`的角色。对`auto`关键词可以添加类型修饰符（cv-限定，指针和引用），和类型修饰符一起，扮演了模板中`ParamType`的角色。

唯一的例外是，`c++11`起引入了用大括号表示的统一初始化，大括号对于非特化的模板来说是无法被推导的，但是可以被`auto`推导为`std::initializer_list`，该类型是一个模板类。

```c++
auto x1 {1}; // std::initializer_list<int>

auto x2 {1, 1.2}; // 无法被推导，因为大括号中两个参数分别为double和int
```

`c++14`起，在函数返回类型上可以使用`auto`，同时也允许在lambda表达式的形参声明上使用`auto`；`c++20`允许一般函数的形参声明也使用`auto`。但是在这些场合上`auto`和模板推导是完全等价的，这意味着以下两段代码是等价的（注意，第二份代码自`c++20`起才符合标准）。

```c++
template<typename T>
T f(T v) { return v; }
```

```c++
auto f(auto v) { return v; } // f({1}) 编译失败
```

