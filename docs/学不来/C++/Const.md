# Const

(CPP4)
(2022年1月25日)

## const

- const默认作用于其左边的东西，否则作用于其右边的东西(C++里 const int* 与 int const* 有什么区别？ - 王国潇的回答 - 知乎<https://www.zhihu.com/question/443195492/answer/1723886545>)
- 允许为常量引用绑定非常量对象
- 使用extern可使得const用于其他文件（但编译器可能替换上的实现会有问题）
- 顶层const：指针本身是常量(const 修饰变量)
`int * const curErr = &errNumb`
- 底层const：所指对象是常量(const 修饰类型)
- `const int *p2=&c1;`
- `const int ci=42; const int &r=ci;` （两个都是底层）
- 同时是：
`const int *const p3= p2;`
- const T& 能够承载右值——字面量——而单纯的T&只能承载一个变量的引用，不能存放一个字面量 (在c++代码中使用const关键字是不是自找麻烦？ - pansz的回答 - 知乎<https://www.zhihu.com/question/473730909/answer/2026149928>)
- const的作用是指明这个变量不能由此更改。因此`const int &j=i;`可以引用到一个变量 （https://stackoverflow.com/questions/44115083/why-can-const-int-bind-to-an-int）
- const 可以用于引用字面值，因此`const double &j=(intval);`是可以的。const引用将会额外创建一个临时变量，并绑定上去。

## constexpr

用于由编译器验证表达式是否是常量表达式

```c++
constexpr int mf=20;
constexpr int lim=mf+1;
constexpr int sz=size(); //size is a constexpr func
```

- 指向指针时，只对指针有效


可以定义递归地constexpr函数：

```c++
constexpr int fibonacci(const int n) {
    return n == 1 || n == 2 ? 1 : fibonacci(n-1) + fibonacci(n-2);
}
```

