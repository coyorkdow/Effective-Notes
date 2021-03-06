# 条款1: 视C++为一个语言联邦

最简单的方法是把C++视为多门相互关联的次级语言的联邦而不是单门语言，在不同的次级语言中遵守不同的准则。主要的次级语言有以下四种。

**1. C。** C++的大量规则仍然是以C语言为基础的。

**2. Object-Oriented。** C++提供了面向对象编程的能力，这是**C with Classes**所诉求的。

**3. Template。** 模板是C++的泛型编程（或者说参数化多态）能力的来源；同时模板为C++提供了强大的元编程能力。

**4. STL。** STL是C++内置的模板基础库。它是C++模板的威力的最简单的体现。学习STL可以不对模板有过深入的了解，但是库作者则必须是模板的专家。

---

**注:** 本条款在今天看来已经比较过时了。`c++11`开始引入的`auto`, 智能指针, 移动语义，lambda等内容难以被归类到上述任意一个次级语言中。这些特性在现代c++的编程准则中被广泛推荐。`auto`和模板推导几乎是一回事；智能指针在几乎所有的场合被推荐代替C语言一样(C-like)的裸指针；移动语义的实现和元编程也密不可分；lambda则根本就是函数式编程的领域。更不要说更崭新的标准`c++20`了。但是这样四大次级语言的划分在“传统”的C++领域仍然是适用的。