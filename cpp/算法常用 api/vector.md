## std::[vector](http://www.cplusplus.com/reference/vector/vector/)::assign

Assign vector content

Assigns new contents to the [vector](http://www.cplusplus.com/vector), replacing its current contents, and modifying its [size](http://www.cplusplus.com/vector::size) accordingly.

|            range (1) | `template <class InputIterator>  void assign (InputIterator first, InputIterator last); ` |
| -------------------: | ------------------------------------------------------------ |
|             fill (2) | `void assign (size_type n, const value_type& val); `         |
| initializer list (3) | `void assign (initializer_list<value_type> il);`             |

```c++
// vector assign
#include <iostream>
#include <vector>

int main ()
{
  std::vector<int> first;
  std::vector<int> second;
  std::vector<int> third;

  first.assign (7,100);             // 7 ints with a value of 100

  std::vector<int>::iterator it;
  it=first.begin()+1;

  second.assign (it,first.end()-1); // the 5 central values of first

  int myints[] = {1776,7,4};
  third.assign (myints,myints+3);   // assigning from array.

  std::cout << "Size of first: " << int (first.size()) << '\n';
  std::cout << "Size of second: " << int (second.size()) << '\n';
  std::cout << "Size of third: " << int (third.size()) << '\n';
  return 0;
}
```