# 结构、联合与枚举

## 结构(`struct`)

`struct` 是任意类型元素的集合。

`struct` 类型的变量可以使用点运算符 `。` 为每个成员分别赋值，也能使用 `{}` 的形式初始化。
> 示例:
>
> ```cpp
> struct Example {
>   int num;
>   const char* name;
> };
> 
> Example ex1;
> ex1.num = 61;
> ex1.name = "Jim Dandy";
>
> Example ex2 = {61, "Jim Dandy"};
> ```

通过指针访问结构的内容是，通常使用 `->` 运算符; 同样也可以通过引用的方式传递和访问结构，通常使用 `.` 运算符访问。
> 如果 p 是一个指针，则 `p->m` 等价于 `*(p).m`。

### `struct` 的布局

在 `struct` 的对象中，成员按照声明的顺序依次存放。 在内存中文成员分配空间时，顺序与声明结构的时候保持一致。
> 需要注意的是，一个 `struct` 对象的大小不一定恰好等于它所有元素大小的累计之和，因为很对硬件对特定类型有着对齐的要求，虽然这样可能会导致结构中存在着空洞。
>
> 所以把成员以大的在前面的尺寸排序可以一定程度上减少空间浪费。

### `struct` 的名字

类型的名字只要一出现就能马上使用了，无需等到该类型的声明全部完成。 但是，结构需要等到 `struct` 的声明全部完成时，才能声明它的对象，这意味着若想要两个或更多的 `struct` 互相引用，必须提前声明好 `struct` 的名字。
> 示例:
>
> ```cpp
> struct List; // 声明结构名字，稍后再定义 List。
> struct Link {
>   Link* pre;
>   Link* suc;
>   List* member_of;
>   int data;
> };
> struct List {
>   Link* head;
> };
> ```
>
> 在这个示例中，尽管 Link 结构还没声明完成，但可以在 Link 结构内使用 Link 类型的指针。 但是如果没有一开始声明 List，之后声明 Link 时使用 List* 类型的指针会造成错误。

### 结构与类

`struct` 是一种特殊的 `class`，只是它的成员默认是 `public` 的，同时，`struct` 也可以包含构造函数等成员函数。
> 如果只需要按照默认的顺序初始化结构成员，就不需要专门定义一个构造函数。
>
> 如果需要改变实参的顺序，检验实参的有效性，修改实参或建立不变时，则应该编写一个专门的构造函数。

### POD(Plain Old Data) 类型

POD 是指能被仅当作数据处理的对象，程序员无需顾局类布局的复杂性以及用户自定义的构造，拷贝和移动语义。
> 这么做主要是为了在硬件条件允许的范围下尽可能高效地移动对象。 例如，如果要执行拷贝含有 100 个元素地数组地任务，调用一百次拷贝构造函数显然不像直接调用 `std::memcpy()` 有效率。

如果想把某个对象仅当作数据处理，则要求该对象必须满足下述的条件:

- 标准布局类型 (Standard layout typr)
- 平凡可拷贝类型 (Triviallt copyable type)
- 具有平凡默认构造函数的类型

> 标准布局类型指的是与 C 语言地布局兼容地类型，并且能被常规地 C++ 应用程序二进制接口处理。
>
> 一般而言，当一个默认构造函数无需执行任何实际操作时，使用 `=default`，那就认为它是平凡的。

标准库 `<type_traits>` 中存在一个属性谓词 `is_pod` 可以判断一个类型是否为 POD。
> 示例:
>
> ```cpp
> template<typename T>
> void mycopy(T* to, const T* from, int count) {
>   if(is_pod<T>::value)
>       memcpy(to,from,count*sizeof(T))
>   else
>       for(int i=0;i!=count:++i)
>           to[i]=from[i];
> }
> ```

### 位域

> 这个部分的内容似乎没什么必要。

用一整个字节来表示一个二元变量似乎是比较浪费的行为，但如果把这些微小的变量组织在一起作为 `struct` 的域，就可以指定成员所占据的位数。
> 示例:
>
> ```cpp
> struct PPN {
>   unsigned int PFN : 22;
>   int : 3;
>   unsigned int CCA : 3;
>   bool nonreachable : 1;
>   bool dirty : 1;
>   bool valid : 1;
>   bool global : 1;
> };
> ```

不过实际上，用域把几个变量打包在一个字节类并不一定能节省空间。 这种做法虽然节省了数据空间，但是负责管理和操作这些变量的代码在绝大多数机器上都会更长。 同时，直接访问 `char` 或 `int` 也比访问位域更快。 位域实际上是用位逻辑运算符从字中提取信息或插入信息的一种便捷手段。

## 联合(`union`)

`union` 是一种特殊的 `struct`，它的所有成员分配在同一个地址空间上。 因此，一个 `union` 实际所占用的空间大小与其最大的成员一样。 自然地，同一时刻 `union` 只能保存一个成员的值。

C++ 本身并不追踪和管理 `union` 中存的是哪一种值，这是程序原需要注意的。

### 联合与类

`union` 是一种特殊的 `struct`，而 `struct` 是一种特殊的 `class`。 但很多提供给 `class` 的功能与联合无关。 包括 `union` 不能含有虚函数，不能含有引用类型的成员，不能含有基类，union 类型的对象不能包含构造函数和析构函数，`union` 不能作为基类。

C++ 允许联合的最多一个成员指定类内初始化器，此时，该初始化器就被用于默认初始化。
> 示例:
>
> ```cpp
> union U {
>   int a;
>   const char* p {""}
> };
>
> U x1; // x1.p: ""
> U x2 {7}; // x2.a: 7
> ```

## 枚举(`enum`)

枚举类型用于存放用户指定的一组整数值,。 枚举类型的每种取值各自对应一个名字，这些值被称为枚举值。

### `enum class`

`enum class` 是一种限定了作用域的强类型枚举。 两个 `enum` 的枚举值不会互相冲突，它们位于各自的 `enum class` 的作用域中。

枚举常用一些整数类型表示，每个枚举值是一个整数。 常把用于表示某个枚举的类型称为基础类型，基础类型默认为 `int`，但是可以显式地指定它:
> 示例:
>
> ```cpp
> enum class Warning : char {green, yellow, orange, red};
> ```
>
> 对 `enum class` 使用 `sizeof()` 的结果是对其基础类型执行 `sizeof()` 的结果。

默认情况下，枚举值从 0 开始，依次递增，但也可以显式地指定它们。
> 示例:
>
> ```cpp
> enum class Printer_flags {
>   acknowledge = 1,
>   paper_empty = 2,
>   busy = 4,
>   out_of_black = 8,
>   out_of_color = 16,
>   //
> };
> ```

枚举类型也是用户自定义类型，因此可以为它定义运算符。
> 示例:
>
> ```cpp
> constexpr Printer_flags operator|(Printer_flags a, Printer_flags b)
> {
>   return static_cast<Printer_flags>(static_cast<int>(a)|static_cast<int>(b))
> }
>
> constexpr Printer_flags operator&(Printer_flags a, Printer_flags b)
> {
>   return static_cast<Printer_flags>(static_cast<int>(a)&static_cast<int>(b))
> }
> ```
>
> 把两个运算符都定义为了 `constexpr` 函数，这样就可以把它们用于常量表达式了。

`enum class` 类型不支持隐式类型转换，所以这里必须使用显式地类型转换。 一个整数类型地值可以显式地转换成枚举类型，只要这个值属于枚举的基础类型的取值范围以内。

C++ 允许先声明一个 `enum class`，再在之后给出它地定义。

### 普通的 `enum`

普通的 `enum` 即 C++ 在提出 `enum class` 之间提供的枚举类型。 普通的 `enum`枚举值位于 `enum` 本身所在的作用域中，它们可以隐式地转化成某些整数类型地值。

一个普通的 enum 还可以是未命名的，如果需要的只是一组整型常量，而不是用于声明变量的类型，则可以使用未命名的 `enum`。
