# C++多线程unique_lock详解

  **C++11中的unique_lock使用起来要比lock_guard更灵活，但是效率会第一点，内存的占用也会大一点。同样，unique_lock也是一个类模板，但是比起lock_guard，它有自己的成员函数来更加灵活进行锁的操作。**

​    **使用方式和lock_guard一样，不同的是unique_lock有不一样的参数和成员函数。它的定义是这样的：**

```javascript
std::unique_lock<std::mutex> munique(mlock);
```

​    **这样定义的话和lock_guard没有什么区别，最终也是通过析构函数来unlock。**

### **std::unique_lock**

​    **unique_lock也可以加std::adopt_lock参数，表示互斥量已经被lock，不需要再重复lock。该互斥量之前必须已经lock，才可以使用该参数。** 

### **std::try_to_lock**

​    **可以避免一些不必要的等待，会判断当前mutex能否被lock，如果不能被lock，可以先去执行其他代码。这个和adopt不同，不需要自己提前加锁。举个例子来说就是如果有一个线程被lock，而且执行时间很长，那么另一个线程一般会被阻塞在那里，反而会造成时间的浪费。那么使用了try_to_lock后，如果被锁住了，它不会在那里阻塞等待，它可以先去执行其他没有被锁的代码。具体实现过程如下：**

```javascript
#include <iostream>
#include <mutex>

std::mutex mlock;

void work1(int& s) {
	for (int i = 1; i <= 5000; i++) {
		std::unique_lock<std::mutex> munique(mlock, std::try_to_lock);
		if (munique.owns_lock() == true) {
			s += i;
		}
		else {
			// 执行一些没有共享内存的代码
		}
	}
}

void work2(int& s) {
	for (int i = 5001; i <= 10000; i++) {
		std::unique_lock<std::mutex> munique(mlock, std::try_to_lock);
		if (munique.owns_lock() == true) {
			s += i;
		}
		else {
			// 执行一些没有共享内存的代码
		}
	}
}

int main()
{
	int ans = 0;
	std::thread t1(work1, std::ref(ans));
	std::thread t2(work2, std::ref(ans));
	t1.join();
	t2.join();
	std::cout << ans << std::endl;
	return 0;
}
```

### **std::defer_lock**

​    **这个参数表示暂时先不lock，之后手动去lock，但是使用之前也是不允许去lock。一般用来搭配unique_lock的成员函数去使用。下面就列举defer_lock和一些unique_lock成员函数的使用方法。**

​    **当使用了defer_lock参数时，在创建了unique_lock的对象时就不会自动加锁，那么就需要借助lock这个成员函数来进行手动加锁，当然也有unlock来手动解锁。这个和mutex的lock和unlock使用方法一样，实现代码如下：**

```javascript
#include <iostream>
#include <mutex>

std::mutex mlock;

void work1(int& s) {
	for (int i = 1; i <= 5000; i++) {
		std::unique_lock<std::mutex> munique(mlock, std::defer_lock);
		munique.lock();
		s += i;
		munique.unlock();         // 这里可以不用unlock，可以通过unique_lock的析构函数unlock
	}
}

void work2(int& s) {
	for (int i = 5001; i <= 10000; i++) {
		std::unique_lock<std::mutex> munique(mlock, std::defer_lock);
		munique.lock();
		s += i;
		munique.unlock();
	}
}

int main()
{
	int ans = 0;
	std::thread t1(work1, std::ref(ans));
	std::thread t2(work2, std::ref(ans));
	t1.join();
	t2.join();
	std::cout << ans << std::endl;
	return 0;
}
```

​    **还有一个成员函数是try_lock，和上面的try_to_lock参数的作用差不多，判断当前是否能lock，如果不能，先去执行其他的代码并返回false，如果可以，进行加锁并返回true，代码如下：**

```javascript
void work1(int& s) {
	for (int i = 1; i <= 5000; i++) {
		std::unique_lock<std::mutex> munique(mlock, std::defer_lock);
		if (munique.try_lock() == true) {
			s += i;
		}
		else {
			// 处理一些没有共享内存的代码
		}
	}
}
```

​    **release函数，解除unique_lock和mutex对象的联系，并将原mutex对象的指针返回出来。如果之前的mutex已经加锁，需在后面自己手动unlock解锁，代码如下：**

```javascript
void work1(int& s) {
	for (int i = 1; i <= 5000; i++) {
		std::unique_lock<std::mutex> munique(mlock);   // 这里是自动lock
		std::mutex *m = munique.release();
		s += i;
		m->unlock();
	}
}
```

### **unique_lock的所有权的传递**

​    **对越unique_lock的对象来说，一个对象只能和一个mutex锁唯一对应，不能存在一对多或者多对一的情况，不然会造成死锁的出现。所以如果想要传递两个unique_lock对象对mutex的权限，需要运用到移动语义或者移动构造函数两种方法。**

**移动语义：**

```javascript
std::unique_lock<std::mutex> munique1(mlock);
std::unique_lock<std::mutex> munique2(std::move(munique1));
// 此时munique1失去mlock的权限，并指向空值，munique2获取mlock的权限
```

**移动构造函数：**

```javascript
std::unique_lock<std::mutex> rtn_unique_lock()
{
	std::unique_lock<std::mutex> tmp(mlock);
	return tmp;
}

void work1(int& s) {
	for (int i = 1; i <= 5000; i++) {
		std::unique_lock<std::mutex> munique2 = rtn_unique_lock();
		s += i;
	}
}
```