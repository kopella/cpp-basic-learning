# C++ 标准库：字符串库的学习笔记与使用技巧

C++ 的字符串库支持类型包括基于模板设计的 `basic_string` 与 C 风格字符串，即空终止符字符串。

标准库在 `<string>` 中提供了 `basic_string` 和相关操作，`<cstring>` 中提供了对 C 风格字符串的支持，在 `<regex>` 中提供了正则表达式匹配组件，在 `<cctype>` 中提供了字符分类操作。

> 本质上，C++ 标准库提供的字符串类型是一个具有常规语义的真正类型，而 C 风格字符串只是由一些有用的函数共同支撑着的一组规范。

在 C++17 中，标准库添加了 `basic_string_view` 类型用来表示自读字符串视图。

## `<string>`

### 类型

| Type                     |                    Definition |
| :----------------------- | ----------------------------: |
| `std::string`            |     `std::basic_string<char>` |
| `std::wstring`           |  `std::basic_string<wchar_t>` |
| `std::u8string` (C++20)  |  `std::basic_string<char8_t>` |
| `std::u16string` (C++11) | `std::basic_string<char16_t>` |
| `std::u32string` (C++11) | `std::basic_string<char32_t>` |

### 常量

`<string>` 库中定义了静态常量 `npos`，这个常量值为类型 `size_type` 的最大值。通常可以用来表示字符串尾。

### 字面值

`operator""s` 可以用来创建 `basic_string` 类型的字面量。与 C 风格字符串字面量的区别在于，它可以包含字符 `'\0'`。

### `basic_string` 构造函数

`basic_string` 提供了各式各样的构造函数，除了默认构造函数以及移动和拷贝构造函数以外，还支持使用 C 风格字符串、迭代器等方法构造

常用构造方法示例：

```cpp
string s0 {}; // 空字符串，等同于 `string s0`
string s1 {"C-style string"} // C 风格字符串构造
string s2 {"C-style string", 7} // "C-style"
string s3 {s1, 0, 7} // s1[0, 0+7)，即 "C-style"
string s4 {s2}; // 拷贝构造，等同于 `string s2 = s1`
string s5 = std::move(s2); // 移动构造
string s6 {7,'a'} // 7 个 'a'
```

> 注意，不要用一个可能是空指针的对象进行初始化，这很可能会导致运行时错误甚至是未定义行为。

### `basic_string` 析构函数

`basic_string` 的析构函数基本只被隐式调用。

### `basic_string` 操作函数

- 元素访问

    `basic_string` 有两种下标访问方式，分别是 `s[i]` 与 `s.at(i)`。其中，通过中括号访问不会进行范围检查，通过 `at()` 函数越界访问会抛出异常。

    使用 `front()` 函数可以访问字符串的首个字符，等价于 `s[0]`；使用 `back()` 函数可以访问字符串的最后一个字符，等价于`s[s.size()-1]`。

    使用 `c_str()` 函数可以获取字符串一个不允许修改的 C 风格版本字符串，在 C++11 后，`date()` 函数拥有了与 `c_str()` 函数完全一致的效果，在之前的 C++ 版本 `date()` 函数会返回指向字符串首字符的指针，是否有空终止符取决于具体的实现。

    > 注意，由于 `basic_string` 可以包含包含字符 `'\0'`，如果对使用 `c_str()` 函数与 `date()` 函数得到的结果执行处理 C 风格字符串的函数（例如 `strcmp()` 函数），则可能出现错误的结果。

    使用 `push_back(c)` 函数可以为字符串末尾追加一个字符 c；使用 `pop_back()` 函数可以清楚字符串的尾字符。

    使用操作符 `+` 可以连接两个字符串，如 `s = s1 + s2`。

    使用 `clear()` 函数将清空字符串。

- 获取子串

    使用 `substr(pos, n)` 函数可以获取一个子串 [pos, pos + n)，如果 pos + n 越过了字符串的结尾，则获取子串 [pos, size())。

- 大小与容量操作

    使用 `size()` 函数与 `length()` 函数可以获取字符串的字符数；使用 `max_size()` 函数可以获取字符串的可能的最大字符数，这个大小取决于系统限制和库的具体实现；使用 `capcity()` 函数可以获取字符串当前分配的储存空间所能保存的最大字符数，即容量；使用 `empty()` 函数可以判断字符串是否为空。

    使用 `resize(n, c)` 函数可以更改存储的字符数到 n，如果 n 小于当前字符数，则缩减字符串至其首 n 个元素，如果 n 大于当前字符数，则额外填充字符 c，使用 `resize(n)` 则默认填充空字符。

    使用 `reserve(n)` 函数可以确保字符串拥有足够的空间保存 n 个字符，如果当前容量不足 n，则会分配新的存储空间，如果容量大于 n，则可能会导致容量收缩。使用 `shrink_to_fit()` 函数会移除未使用的存储空间，让容量和大小相等，这是 C++11 中添加的函数。

    > 注意，若字符串容量发生变化，则所有的迭代器与引用都会被非法化。

- 比较

    使用比较操作符可以按字典序比较字符串。

    还能使用 `compare(s)` 函数可以按字典序比较字符串；使用 `compare(pos, n, s)` 函数可以用子串 [pos, pos + n) 和字符串 s 按字典序比较；使用  `compare(pos, n, s，s_pos, s_n)` 函数可以用子串 [pos, pos + n) 和字符串 s 的子串 [s_pos, s_pos + s_n) 按字典序比较。

- 类 STL 操作

    `basic_string` 的成员类型和函数符合要求，因此也可以使用标准库提供的迭代器和容器算法。

### I/O

使用流操作符 `<<` 可以输出字符串，以及使用 `>>` 可以读取输入到字符串中。

使用 `getline(in, s)` 函数会从输入流 in 中读取字符存入字符串 s 中，直到遇到结束符（默认为 `'\n'`），结束符将被删除，不会被存入字符串。使用 `getline(in, s，d)` 函数可以将结束符自定义为字符 d。

> 一个逐行读取的使用示例：
>
> ```cpp
> vector<string> lines;
> for(string s; getline(cin,s);)
>     lines.push_back(s);
> ```

### 数值转换

`<string>` 库中提供了一组函数，可以从一个字符串内容是一个数值的字符串表示的 `string` 或 `wstring` 中抽取出数值。函数名形式接近 `sto{type}(s, pos, base)`，函数名中的 `{type}` 表示转化的类型，参数 s 为要抽取数值的字符串， pos 会存储表示已处理字符数的整数的地址，base 为数的基。

> 一个抽取 int 的使用示例：
>
> ```cpp
> std::string str_dec = "2001, A Space Odyssey";
> std::string::size_type sz;   // alias of size_t
> int i_dec = std::stoi (str_dec,&sz);
> std::cout << str_dec << ": " << i_dec << " and [" << str_dec.substr(sz) << "]\n";
> // Output:
> // 2001, A Space Odyssey: 2001 and [, A Space Odyssey]
> ```

使用 `to_string(x)` 函数与 `to_wstring(x)` 函数可以将一个整数或浮点值转化为 `string` 与 `wstring`。

## 注意汇总

- 不要用一个可能是空指针的对象进行初始化，这很可能会导致运行时错误甚至是未定义行为。
- 由于 `basic_string` 可以包含包含字符 `'\0'`，如果对使用 `c_str()` 函数与 `date()` 函数得到的结果执行处理 C 风格字符串的函数（例如 `strcmp()` 函数），则可能出现错误的结果。
- 若字符串容量发生变化，则所有的迭代器与引用都会被非法化。

## 使用建议

- 优先选择 `string_stream` 或通用的值抽取函数（例如 `to<X>`）而非直接使用 `sto*()` 系列数值转换函数。
- 优先使用 `find()` 操作在 string 中定位元素而不是自己编写循环。
- 一般使用 `substr()` 读取子串，用 `repalce()` 写入子串。
