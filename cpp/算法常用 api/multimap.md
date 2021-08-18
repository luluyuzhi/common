# multimap

## 初始化及遍历

```c++
int main()
{
	std::multimap mm{  {std::make_pair(1,2),{1,4} } };
	//   std::multimap m2(m1.begin(), m1.end()); // guide #1
	mm.insert({ 1,3 });

	for (auto& itr : mm)
	{
		std::cout << "first: " << itr.first << "second: " << itr.second << std::endl;
	}
}
```



```
first: 1 second: 2
first: 1 second: 4
first: 1 second: 3
```

1. c++17 的新特性通过 构造函数，反推类型模板参数

2. muitlmap 的遍历



通过 红黑树实现

## 范围迭代器

找到key 等于3的一个范围迭代器， 如果要寻找的 key 不存在，则 返回的迭代器 first == containor::end() 为真。

```c++
auto t = mm.equal_range(3);

//assert(t.first != mm.end());
while (t.first != t.second)
{
    std::cout << t.first->first << t.first->second << std::endl;
    t.first++;
}
```



## `emplace` 与 `insert`

`emplace` 与 `insert` 均返回指向插入位置的迭代器，与 `map` 不同 emplace 与 insert 是一定能插入成功的。

## `emplace`与 `emplace_hint`



map 的成员函数 `emplace_hint`和 `emplace` 生成元素的方式在本质上是一样的，除了必须为前者提供一个指示元素生成位置的迭代器，作为 `emplace_hint`的第一个参数。这个参数的意义在于尽量靠近待插入节点的位置。便于更快的找到插入的位置。

但也可以随便给定一个 迭代器，此举带来的后果就是性能有影响。(顺序迭代器与直接从红黑树找到节点来比价性能。)

```c++
auto itr = mm.emplace(1, 10);

auto itr2 = mm.emplace(2, 13);

mm.emplace_hint(itr2, 1, 11);

for (auto& itr : mm)
{
    std::cout << "first: " << itr.first << " second: " << itr.second << std::endl;
}
```

## erase

不多说， 迭代器 ， key ， range



## extract

根据迭代器与key 从容器中拿出 node。 也就是 node_type 类型 。像 map  multimap 这种树的数据结构都有 node_type 。我们可以拿到 extract 后的节点 ，然后进行 `insert` 操作。 



```c++
#include <iostream>
#include <map>
int main()
{
	std::multimap mm{  {std::make_pair(1,2),{1,4} } };

	auto t0 =mm.insert({ 1,3 });


	for (auto& itr : mm)
	{
		std::cout << "first: " << itr.first << " second: " << itr.second << std::endl;
	}

	auto t1 = mm.extract(t0);

	std::cout << "key: " << t1.key() << " mapped:" << t1.mapped() << std::endl;

	for (auto& itr : mm)
	{
		std::cout << "first: " << itr.first << " second: " << itr.second << std::endl;
	}
}
```

## merge

可以合并一个 map 或者 multimap 。原来的数据全部被移走

## 其他



```c++
contains (C++20)
checks if the container contains element with specific key

equal_range
returns range of elements matching a specific key

lower_bound
Returns an iterator pointing to the first element that is not less than (i.e. greater or equal to) key.

upper_bound 
Returns an iterator pointing to the first element that is greater than key.
```

```c++
#include <iostream>
#include <map>
 
int main()
{
    std::multimap<int,char> example = {{1,'a'},{2,'b'}};
 
    for(int x: {2, 5}) {
        if(example.contains(x)) {
            std::cout << x << ": Found\n";
        } else {
            std::cout << x << ": Not found\n";
        }
    }
}
```



# unordered_set/map 与 unordered_ multiset/multimap

**他们都是通过 hash 表来创建， 解决冲突的方式都是通过链地址法。**

在内部，unordered_set中的元素并没有按照任何特定的顺序排序，而是根据它们的散列值组织成**桶**，从而允许直接通过它们的值快速访问单个元素(平均平均时间复杂度不变)。

哈希桶算法其实就是链地址解决冲突的方法。
