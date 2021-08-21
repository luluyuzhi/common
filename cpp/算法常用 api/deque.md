### deque

```c++
#include <deque>
deque<int> s1;
//a) 构造函数
deque<int> ideq
//b)增加函数
 ideq.push_front(x):双端队列头部增加一个元素X
 ideq.push_back(x):双端队列尾部增加一个元素x
//c)删除函数
ideq.pop_front():删除双端队列中最前一个元素
ideq.pop_back():删除双端队列中最后一个元素
ideq.clear():清空双端队列中元素
//d)判断函数
ideq.empty() :向量是否为空，若true,则向量中无元素
//e)大小函数
ideq.size():返回向量中元素的个数
```

### std::set

```c++
std::pair<iterator,bool> insert( const value_type& value );
std::pair<iterator,bool> insert( value_type&& value );
iterator insert( iterator hint, const value_type& value ); 
iterator insert( const_iterator hint, const value_type& value );
iterator insert( const_iterator hint, value_type&& value );

// find
template< class K > iterator find( const K& x );	// 14

```

