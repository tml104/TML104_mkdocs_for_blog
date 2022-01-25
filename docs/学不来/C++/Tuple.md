# Tuple

(CPP5)
(2022年1月25日)

<https://changkun.de/modern-cpp/zh-cn/02-usability/#2-3-%E7%B1%BB%E5%9E%8B%E6%8E%A8%E5%AF%BC>

```c++
#include <iostream>
#include <tuple>

std::tuple<int, double, std::string> f() {
    return std::make_tuple(1, 2.3, "456");
}

int main() {
    auto [x, y, z] = f();
    std::cout << x << ", " << y << ", " << z << std::endl;
    return 0;
}
```

## 定义

```c++
std::tuple<T1, T2, TN> t1;            //创建一个空的tuple对象（使用默认构造），它对应的元素分别是T1和T2...Tn类型，采用值初始化。

std::tuple<T1, T2, TN> t2(v1, v2, ... TN);    //创建一个tuple对象，它的两个元素分别是T1和T2 ...Tn类型; 要获取元素的值需要通过tuple的成员get<Ith>(obj)进行获取(Ith是指获取在tuple中的第几个元素，请看后面具体实例)。

std::tuple<T1&> t3(ref&); // tuple的元素类型可以是一个引用

std::make_tuple(v1, v2); // 像pair一样也可以通过make_tuple进行创建一个tuple对象
```


## 获取元素个数: tuple_size::value

```c++

int a=2;
int b=5;
tuple<int&,int&> p1{a,b};

cout<<(std::tuple_size<decltype(p1>::value)<<endl;

```

## 获取元素内容： get

```c++
std::get<0>(p1);
```

- tuple无法迭代：给定的索引必须是constexpr


## 获取元素的类型： tuple_element::type

```c++

std::tuple<std::string, int> tp("Sven", 20);
 
// 得到第二个元素类型
 
std::tuple_element<1, decltype(tp)>::type ages;  // ages就为int类型
 
ages = std::get<1>(tp);
 
std::cout << "ages: " << ages << '\n';

```


## 解包元素： tie

```c++

int c,d,e;
std::tie(c,d) = p1;
std::tie(std::ignore,e) = p1;

cout<<c<<" "<<d<<endl;
cout<<e<<endl;
```

- 使用 `std::ignore` 占位


## 设置tuple的内容引用以便通过其他引用变量更改内容

```c++

    std::tuple<std::string, int, float> tp1("Sven Cheng", 77, 66.1);
 
    std::string name;
    int weight;
    float f;
 
    auto tp2 = std::make_tuple(std::ref(name), std::ref(weight), std::ref(f)) = tp1;

```