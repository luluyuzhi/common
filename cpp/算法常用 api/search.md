### **1.头文件**

```
#include <algorithm>
```

### **2.使用方法**

**a.binary_search：查找某个元素是否出现。**

a.函数模板：`binary_search(arr[],arr[]+size , indx)`

b.参数说明：
  arr[]： 数组首地址
  size：数组元素个数
  indx:需要查找的值

c.函数功能： 在数组中以二分法检索的方式查找，若在数组(要求数组元素非递减)中查找到indx元素则真，若查找不到则返回值为假。

**2.lower_bound：查找第一个大于或等于某个元素的位置。**
a.函数模板：`lower_bound(arr[],arr[]+size , indx):`
b.参数说明：
  arr[]： 数组首地址
  size：数组元素个数
  indx:需要查找的值
c.函数功能： 函数lower_bound()在first和last中的前闭后开区间进行二分查找，返回大于或等于val的第一个元素位置(注意是地址)。如果所有元素都小于val，则返回last的位置
d.举例如下：
　　一个数组number序列为：4,10,11,30,69,70,96,100.设要插入数字3,9,111.pos为要插入的位置的下标，则
　　/*注意因为返回值是一个指针，所以减去数组的指针就是int变量了*/
　　pos = lower_bound( number, number + 8, 3) - number，pos = 0.即number数组的下标为0的位置。
　　pos = lower_bound( number, number + 8, 9) - number， pos = 1，即number数组的下标为1的位置（即10所在的位置）。
　　pos = lower_bound( number, number + 8, 111) - number， pos = 8，即number数组的下标为8的位置（但下标上限为7，所以返回最后一个元素的下一个元素）。
e.注意：函数lower_bound()在first和last中的前闭后开区间进行二分查找，返回大于或等于val的第一个元素位置。**如果所有元素都小于val，则返回last的位置，且last的位置是越界的！**

　　返回查找元素的第一个可安插位置，也就是“元素值>=查找值”的第一个元素的位置

**3.upper_bound：查找第一个大于某个元素的位置。**
a.函数模板：`upper_bound(arr[],arr[]+size , indx):`
b.参数说明：
  arr[]： 数组首地址
  size：数组元素个数
  indx:需要查找的值
c.函数功能：函数upper_bound()返回的在前闭后开区间查找的关键字的上界，返回大于val的第一个元素位置
　　例如：一个数组number序列1,2,2,4.upper_bound(2)后，返回的位置是3（下标）也就是4所在的位置,同样，**如果插入元素大于数组中全部元素，返回的是last。(注意：数组下标越界)**
　　返回查找元素的最后一个可安插位置，也就是“元素值>查找值”的第一个元素的位置。