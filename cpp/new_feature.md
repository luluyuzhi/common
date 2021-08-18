```c++
// 用于给字符串加双引号
#include<iomanip>
std::quoted
```

```c++
// 判断 key 是否存在 unordered_set
std::unordered_set::count
size_type count ( const key_type& k ) const;
// Searches the container for elements with a value of k and returns the number of elements found. Because unordered_set containers do not allow for duplicate values, this means that the function actually returns 1 if an element with that value exists in the container, and zero otherwise.
// 如果有返回 1， 否则返回0（因为是set）
```

```c++
std::vector::vector
vector (size_type n, const value_type& val,
                 const allocator_type& alloc = allocator_type());
// fill constructor
// Constructs a container with n elements. Each element is a copy of val (if provided).
```

```c++
#include <string>
std::string to_string( int value );
std::string to_string( long value );
std::string to_string( long long value );
std::string to_string( unsigned value );
std::string to_string( unsigned long value );
std::string to_string( unsigned long long value );
std::string to_string( float value );
std::string to_string( double value );
std::string to_string( long double value );

1) Converts a signed integer to a string with the same content as what
 std::sprintf(buf, "%d", value) would produce for sufficiently large buf.
2) Converts a signed integer to a string with the same content as what
 std::sprintf(buf, "%ld", value) would produce for sufficiently large buf.
3) Converts a signed integer to a string with the same content as what
 std::sprintf(buf, "%lld", value) would produce for sufficiently large buf.
4) Converts an unsigned integer to a string with the same content as what
 std::sprintf(buf, "%u", value) would produce for sufficiently large buf.
5) Converts an unsigned integer to a string with the same content as what
 std::sprintf(buf, "%lu", value) would produce for sufficiently large buf.
6) Converts an unsigned integer to a string with the same content as what
 std::sprintf(buf, "%llu", value) would produce for sufficiently large buf.
7,8) Converts a floating point value to a string with the same content as what
 std::sprintf(buf, "%f", value) would produce for sufficiently large buf.
9) Converts a floating point value to a string with the same content as what
 std::sprintf(buf, "%Lf", value) would produce for sufficiently large buf.
```

