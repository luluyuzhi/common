这里先介绍find, find_if, find_first_of，三个函数。其余的以后再更新。

### std::find()

用法：find(first, end, value);

返回区间[first，end）中第一个值等于value的元素位置；若未找到，返回end。函数返回的是迭代器或指针，即位置信息。

###  std::find_if()

用法：find_if(first, end, bool pred);

返回区间[first，end)中使一元判断式pred为true的第一个元素位置；若未找到，返回end。

### std::find_first_of()

用法：find_first_of(first1, end1, first2, end2);

 返回第一个区间迭代器位置，满足第一个区间[first1，end1)中的元素第一次出现在第二个区间[first2，end2)中。

通俗就是说顺序从第一个区间 [first1,end1) 中取一个元素，在第二个区间中找有没有相同的元素，**如果有就返回第一个区间中元素位置；**未找到则继续。**用第一个区间的下一个元素在第二个区间找。**

因此只要保证两个区间内的元素可以==即可，不用管装元素的容器形式。下面的例子我第一个区间用的vector、而第二个区间是list也是可以的。