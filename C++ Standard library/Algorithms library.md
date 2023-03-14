# C++ 标准库：算法库的学习与使用

C++ 的算法库 `<algorithm>` 中定义了大约八十个标准算法。这些算法的操作基于迭代器进行，且完全不了解迭代器所指向的容器的数据结构，大多数的算法返回值也是迭代器。迭代器将算法从它所处理的数据结构上分离了下来。

## 执行策略

在 C++17 以后，可以为算法设置使用的执行策略，以指示算法的执行是否可以并行化、向量化。

标准库的执行策略被定义在 `<execution>` 中，在 C++17 中定义了三种执行策略，在 C++20 中新增了一种执行策略。

## 不修改序列的算法

不修改序列的算法只读取输入序列中元素的值，不会重排序列。

未特别说明的情况下，以下标准算法中，总是接受一对表示序列开始与结尾的迭代器作为输入；如果算法需要策略操作实参，则普通版本函数接受特定值作为参数，而函数名带有 _if 后缀的版本接受谓词作为参数。

- `for_each` e.g. `for_each(b, e, f)`

`for_each` 是最简单的算法，能对序列中的每个元素执行谓词指定的操作。

- `all_of` e.g. `all_of(b, e, f)`
- `any_of` e.g. `any_of(b, e, f)`
- `none_of` e.g. `none_of(b, e, f)`

这三个函数被称为序列谓词，将检查函数接受的谓词是否对输入序列中所有、任意或不存在元素为 `true`。

如果成功，函数将返回 `true`，失败则返回  `false`。

- `count` e.g. `count(b, e, v)`
- `count_if` e.g. `count_if(b, e, f)`

该函数用于获取满足判别标准的元素数。

- `equal` e.g. `equal(b, e, b2)`, `equal(b, e, b2, f)`

该函数用于判断两个序列中的所有对应元素是否都相等，作为输入的第二个序列只需要一个表示开始的迭代器。

该函数可以接收谓词，否则将调用 `operator==` 进行比较。

### 查找算法

查找算法如果查找成功，会返回指向输入序列被查找到的第一个元素的迭代器，失败则返回输入序列的末尾迭代器。

对于接受特定值查找的算法，函数将调用 `operator==` 进行比较；如果使用了二元谓词，函数将查找使谓词返回 `true` 的元素。

- `find` e.g. `find(b, e, v)`
- `find_if` e.g. `find_if(b, e, f)`
- `find_if_not` e.g. `find_if_not(b, e, f)`

这些函数用于寻找**首个**满足特定判别标准的元素。

`find_if_not` 是 C++11 中新增的函数，就效果而言，和以取反的谓词使用 `find_if` 等价。

- `find_first_of` e.g. `find_first_of(b, e, b2, e2)`, `find_first_of(b, e, b2, e2, f)`

该函数接受**两对**表示序列开始与结尾的迭代器作为输入，查找两个序列中首个相等的元素，并返回指向第一个序列查找到的元素的迭代器。

该函数可以接收谓词。

- `find_end` `find_end(b, e, b2, e2)`, `find_end(b, e, b2, e2, f)`

该函数接受**两对**表示序列开始与结尾的迭代器作为输入，查找两个序列中最后一个相等的元素，并返回指向第一个序列查找到的元素的迭代器。

该函数可以接收谓词。

- `adjacent_find` e.g. `adjacent_find(b, e)`, `adjacent_find(b, e, f)`

该函数将寻找**首对**相邻的相同元素。

该函数可以接收谓词。

- `search` `search(b, e, b2, e2)`, `search(b, e, b2, e2, f)`

该函数接受**两对**表示序列开始与结尾的迭代器作为输入，查找输入的第二个序列是否是第一个序列的子序列，如果是，则返回指向第一个序列中匹配子序列的第一个元素的迭代器。

该函数可以接收谓词。

- `search_n` e.g. `search(b, e, n, v)`, `search(b, e, n, v, f)`

该函数接受**一对**表示序列开始与结尾的迭代器作为输入，查找一定量的某个元素的连续子序列。

该函数可以接收谓词。

- `mismatch` e.g. `mismatch(b, e, b2)`, `mismatch(b, e, b2, f)`

该算法查找两个序列中第一对不匹配的元素，并以 `std::pair` 的形式返回指向这两个元素的迭代器。

该函数接受**一对**表示序列开始与结尾的迭代器作为输入，同时需要第二个序列表示开始的迭代器作为输入。

## 修改序列的算法

修改序列的算法，又称可变序列算法，可以修改其实参序列的元素。

未特别说明的情况下，以下标准算法中，总是接受一对表示序列开始与结尾的迭代器作为输入；如果算法输出序列，则总是接受一个表示序列开始的迭代器作为输出；如果算法需要策略操作实参，则普通版本函数接受特定值作为参数，而函数名带有 _if 后缀的版本接受谓词作为参数。

- `transform` e.g. `transform(b, e, out, f)` `transform(b, e, b2, out, f)`

该算法能对输入序列中的每一个元素执行指定的谓词操作，然后将结果写入指定输出的序列中，并返回指向输出序列末尾的迭代器。

该函数接受**一对**表示序列开始与结尾的迭代器作为输入，此时使用的谓词必须是一元谓词。

该函数在接受**一对**表示序列开始与结尾的迭代器作为输入外，还能在接受另一个表示序列开始的迭代器作为输入，此时使用的谓词必须是二元谓词。

- `copy` e.g. `copy(b, e, out)`
- `copy_if` e.g. `copy_if(b, e, out, f)`
- `copy_n` e.g. `copy(b, n, out)`
- `copy_backward` e.g. `copy_backward(b, e, out)`

该算法能将一个序列拷贝元素至另一个序列。

- `move` e.g. `move(b, e, out)`
- `move_backward` e.g. `move_backward(b, e, out, f)`

该算法能将一个序列移动元素至另一个序列。

- `unique` e.g. `unique(b, e)`, `unique(b, e, f)`

该算法能将序列连续的重复值移动到序列末尾，并返回指向第一个重复元素的迭代器。

需要注意的是，`unique` 函数实际上并不会删除元素或是修改容器的大小，因此调用 `unique` 后有时会需要调用容器的 `erase` 成员函数，这将擦除未指定值并减小容器的物理大小，以匹配它新的逻辑大小。

- `unique_copy` e.g. `unique_copy(b, e, out)`, `unique_copy(b, e, out, f)`

该算法将去除了连续的重复值序列拷贝至另一个序列。

- `remove` e.g. `remove(b, e, v)`
- `remove_if` e.g. `remove_if(b, e, f)`
- `remove_copy` e.g. `remove(b, e, out, v)`
- `remove_copy_if` e.g. `remove_copy_if(b, e, out, f)`

该算法能将指定值移动到序列末尾，并返回指向第一个删除元素的迭代器。

需要注意的是，和 `unique` 一样，该函数实际上也不会删除元素或是修改容器的大小。

- `reverse` e.g. `reverse(b, e)`

该算法能将序列中的元素逆序排列

- `replace` e.g. `replace(b, e, v， v2)`
- `replace_if` e.g. `replace_if(b, e, f, v2)`
- `replace_copy` e.g. `replace_copy(b, e, v， v2)`
- `replace_copy_if` e.g. `replace_copy_if(b, e, f, v2)`

该算法能将满足判别标准的元素替换为其他值。

- `rotate` e.g. `rotate(b, m, e)`
- `rotate_copy` e.g. `rotate_copy(b, m, e, out)`

将序列视作首位相接的一个环，旋转序列元素，使被给予指向中间元素的元素位于序列开始。

- `random_shuffle` e.g. `random_shuffle(b, e)`, `random_shuffle(b, e, f)`

洗牌序列中的元素，若无给予的随机数发生器，则使用默认随机数发生器。

- `shuffle` e.g.