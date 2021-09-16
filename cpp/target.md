## C/C++ 内存对齐原则及作用

在C++中每个类型都有两个属性，一个是大小（size），还有一个就是对齐要求（alignment requirement），或称之为对齐量（alignment）。C++标准并没有规定每个类型的对齐量，但是一般都会有这样的规律。

1. 所有基础类型的对齐量等于这个类型的大小。
2. struct, class, union类型的对齐量等于他的非静态成员变量中最大的对齐量。

另外，标准规定所有的对齐量必须是2的幂。

## 在C++中修改对齐要求

一般情况下，我们不需要自定义对齐要求，但也会有很特殊的情况下需要做调整。C++中，我们可以使用alignas关键字修改一个类型、或者一个变量的对齐要求。例如：

```
class MyObject
{
    char c;
    alignas(8) int i;
    short s;
};
```

这样的话，变量i的对齐要求由原本的4变成了8，结果就是，i的字节数偏移由4变成了8，s的字节数偏移由8变成了12，MyObject的对齐要求也变成了8，大小变成了16。

我们也可以对MyObject的定义使用alignas：

```
class alignas(16) MyObject
{
    char c;
    int i;
    short s;
};
```

还可以在alignas里面写某个类型。也可以使用多个alignas，结果就是使用最大的对齐要求。例如以下MyObject的对齐要求就是16：

```
class alignas(int) alignas(16) MyObject
{
    char c;
    int i;
    short s;
};
```

alignas有一个限制，那就是不能用alignas改小对齐要求。例如以下的代码会报错：

```
alignas(1) int i;
```

另外，C++中，有一个特殊的类型：max_align_t，所有不大于他的对齐量叫做基础对齐量（fundamental alignment），比这个对齐量大的叫做扩展对齐量（extended alignment ）。C++标准规定，所有平台必须要支持基础对齐量，而对于扩展对齐量的支持要看各个平台。一般来说max_align_t的对齐量等于long double的对齐量。

