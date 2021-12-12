# 条款3: 尽可能使用const

使用`const`修饰符能让编译器帮忙检查变量是否有可能被修改，这能让很多潜在的bug在编译时就被检测出来。

使用`const`的问题主要在于它的强传染性。一个指针或引用变量指涉了`const`修饰的型别，如果想将它传入某个函数，则该函数的形参指涉的型别也必须为`const`。

```c++
void f(int&);
void g(int*);

const int x = 0;
f(x); // 无法编译，需要提供重载版本void f(const int&)
g(&x) // 无法编译，需要提供重载版本void g(const int*)
```

这种传染性的更好的体现在于，如果把一个类实例变量修饰为`const`，则此时这个实例的任何成员变量都不能被修改，而它也仅能调用有`const`修饰的成员函数。这迫使一些成员函数需要提供针对`const`的重载版本。如果`Container`不提供`const`版本的`Get`，则它的`cosnt`实例变量无法调用该成员函数；反之如果只提供`const`版本，那么非`const`实例变量无法通过`Get`修改`vec_`。

```c++
template<typename T>
class Container {
 private:
  std::vector<T> vec_;
 public:
  T& Get(size_t i) { return vec_[i]; }
  const T& Get(size_t i) const { return vec_[i]; }
};
```

重复造轮子是不被提倡的，这种功能完全一样的函数重载应该复用其中一方的代码，`const_cast`被用来解决这个问题。`const_cast`是唯一的将const对象转化为非const对象的方法。如以下代码所示。

```c++
T& Get(size_t i) {
  return const_cast<T&>(static_cast<const Container<T>&>(*this).Get(i));
}
```

有些时候，我们希望一些成员变量即使在`const`修饰的实例变量里也能被修改，使用`mutable`修饰这样的变量即可。

---

**注1:** 对于指针来说，指针本身是`const`和指涉的型别是`const`是两个概念。自身是`const`又叫“顶层const”，而另一种则叫“底层const”。声明“顶层const”的方法是把`const`加在指针符号的后面。

```c++
const int* ptr1; // ptr1指向const int，但它自己不是const
int* const ptr2; // ptr2是const的，但是它指涉的变量可以被修改
const int* const ptr3; // 指向const int的const指针
const int *
```

但是即使知道了这条规则，形如`const int ** const *`这样多层指针的型别仍然令人困扰（它的指涉关系是：非const指针->const指针->非const指针->`const int`）。这个时候使用`c++11`提供的`using`类型别名会大大提升代码可读性。

**注2:** 看起来`const_cast`能强制修改`const`修饰的变量的值，但是这种行为其实是未定义的。
