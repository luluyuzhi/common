[STL中nth_element的用法](https://www.cnblogs.com/xenny/p/9424894.html)

### mutilmap



```c++
template< class InputIt1, class InputIt2>
bool equal( InputIt1 first1, InputIt1 last1, InputIt2 first2 );
```

### priority_queue

```c++
//升序队列
priority_queue <int,vector<int>,greater<int> > q;
//降序队列
priority_queue <int,vector<int>,less<int> >q;

#include <queue>

// top 访问队头元素
// empty 队列是否为空
// size 返回队列内元素个数
// push 插入元素到队尾 (并排序)
// emplace 原地构造一个元素并插入队列
// pop 弹出队头元素
// swap 交换内容

priority_queue<pair<int, int> > a;
```

### std::[string](https://www.cplusplus.com/reference/string/string/)::resize

将字符串调整为 n 个字符的长度。

如果 n 小于当前字符串长度，则将当前值缩短为其前 n 个字符，删除第 n 个之后的字符。

如果 n 大于当前字符串长度，则通过在末尾插入尽可能多的字符来扩展当前内容，以达到 n 的大小。 如果指定了 c，则新元素被初始化为 c 的副本，否则，它们是值初始化字符（空字符）。

### std::reverse(beg,end)

reverse()会将区间[beg,end)内的元素全部逆序； 

### this_thread::yield();

将时间片让给其他线程。

