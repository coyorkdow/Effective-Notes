# 条款1: 理解模板型别推导

考虑以下伪代码

```c++
template<typename T>
void f(ParamType param);
```

一次形如`f(expr)`的调用会让编译器推导出两个型别：`T`和`ParamType`，`ParamType`可以与`T`不同，通常前者包含了cv限定符(`const`和`volatile`)以及指针/引用符号。

如果`ParamType`为引用或指针，`T`的推导结果可以包含cv限定符，否则不行。

对于如下情况

```c++
const int* const a = nullptr;
f(a);
```

若`ParamType`为`T`，则`T`型别为`const int *`，这个型别本身不具有`const`限定，而是指涉了具有`const`限定的型别。

若`ParamType`为`T*`，则`T`型别为`const int`。

若`ParamType`为`T&`，则`T`型别为`const int * const`，这个型别指涉了具有`const`限定的型别，同时本身也有`const`限定。

若`ParamType`为`T*&`，则模板无法匹配。因为`a`具有`const`限定而`ParamType`没有，如果改为`const T*&`方可。`T`的型别是`const int`，为指针指涉的型别；`ParamType`的限定符匹配指针本身的`const`限定。

如果`ParamType`为万能引用（`ParamType`为`T&&`），则接受左值引用时`T`型别为左值引用，根据引用折叠`ParamType`型别为左值引用。详见[C++移动语义: 右值引用, 引用折叠, std::move, std::forward](https://coyorkdow.github.io/c++/2021/07/11/C++_move.html)。

如果`ParamType`不为引用，则传递数组或函数型别时会退化为数组或函数指针。对于如下情况

```c++
const int a[1] = {1};
f(a);
```

若`ParamType`为`T&`，则`T`型别为`const int[1]`；若`ParamType`为`T`或`T*`，则`a`在传参时退化为指针`const int*`，推导结果可见上文。