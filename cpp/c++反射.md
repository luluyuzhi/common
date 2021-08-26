# 我所理解的C++反射机制

# 1.前言

在实际的项目中，听到师兄说C++中用到了反射，出于好奇，就查阅相关资料，发现强大的C++本身并不支持反射，反而Java支持反射机制。当我得知这个事实时，一直唯C++马首是瞻的我，心中暗自落泪，悲叹不已。但是，C++的fans别难受，强大的C++本身不支持，但却可以让我们手动实现，真的是曲径通幽处，禅房花木深。C++是不会辜负我们对它的至死不渝的热枕与追逐。

但是，说到Java的反射机制或者C++用到了反射，如果没有真正的在项目中使用过，我们对它会感觉到陌生和不解。我们会问反射是什么，反射的作用是什么，反射的应用场景又是什么？在你查看了很多资料，可能还是会有疑惑，要不就是被那些官方的术语忽悠的晕头转向，不知东南西北，要不就是被博文作者拙劣的文字和陌生的应用场景弄的丈二和尚摸不着头脑，不知所云。下面我就提一个简单的应用场景，以此作为讲解C++反射机制的实际用处的切入点。遇到问题，才去探索问题的解决方法，解决问题之后，我们就学到了新的知识。这就是我们常用的，习以为常的学习模式，符合常人的学习求知习惯。如果，我们不知道反射能解决什么问题，或者说我们在工作实践中遇到的问题无需反射来解决，那么我们千辛万苦，煞费苦心去学习这个不常用的东西，意义何在呢？

所以，这里抛出一个问题：**如何通过类的名称字符串来生成类的对象。比如有一个类ClassA，那么如何通过类名称字符串”ClassA”来生成类的对象呢？**

在Java编程中，会经常要用到反射，但是我想很多使用C++的人至今都没有想过这个问题。C++是不支持通过类名称字符串”ClassXX”来生成对象的，也就是说我们可以使用`ClassXX* object =new ClassXX;` 来生成对象，但是不能通过`ClassXX* object=new "ClassXX";` 来生成对象。

那么我们如何解决这个问题呢？我们就可以通过反射来解决这个问题。找到了问题所在，找到了反射能够解决实际的问题，是不是觉得充满了动力和期待去学习C++如何实现反射来解决这个问题呢？

好了，我们知道了反射能够解决这个问题，但什么是反射呢？我们来个百度百科较官方的定义：**反射是程序可以访问、检测和修改它本身状态或行为的一种能力。**有点抽象，我的理解就是程序在运行的过程中，可以通过类名称创建对象，并获取类中申明的成员变量和方法。

言归正传，我们如何解决上面提出的问题呢？下面我们就慢慢讲解C++中实现反射来解决上面的问题。

# 2.具体设计与实现

## 2.1设计思路

我的设计思路大致是这样的。  

（1）为需要反射的类中定义一个创建该类对象的一个回调函数；

（2）设计一个工厂类，类中有一个std::map，用于保存类名和创建实例的回调函数。通过类工厂来动态创建类对象；

（3）程序开始运行时，将回调函数存入std::map（哈希表）里面，类名字做为map的key值；

实现流程如下图所示：  



## 2.2具体实现

下面我来一步一步的讲解具体的实现方法。

**第一步：**定义一个函数指针类型，用于指向创建类实例的回调函数。

```javascript
typedef void* (*PTRCreateObject)(void);  
```

**第二步：**定义和实现一个工厂类，用于保存保存类名和创建类实例的回调函数。工厂类的作用仅仅是用来保存类名与创建类实例的回调函数，所以程序的整个证明周期内无需多个工厂类的实例，所以这里采用单例模式来涉及工厂类。

```javascript
//工厂类的定义
class ClassFactory{
private:  
    map<string, PTRCreateObject> m_classMap ;  
    ClassFactory(){}; //构造函数私有化

public:   
    void* getClassByName(string className);  
    void registClass(string name, PTRCreateObject method) ;  
    static ClassFactory& getInstance() ;  
};

//工厂类的实现

//@brief:获取工厂类的单个实例对象  
ClassFactory& ClassFactory::getInstance(){
    static ClassFactory sLo_factory;  
    return sLo_factory ;  
}  

//@brief:通过类名称字符串获取类的实例
void* ClassFactory::getClassByName(string className){  
    map<string, PTRCreateObject>::const_iterator iter;  
    iter = m_classMap.find(className) ;  
    if ( iter == m_classMap.end() )  
        return NULL ;  
    else  
        return iter->second() ;  
}  

//@brief:将给定的类名称字符串和对应的创建类对象的函数保存到map中   
void ClassFactory::registClass(string name, PTRCreateObject method){  
    m_classMap.insert(pair<string, PTRCreateObject>(name, method)) ;  
}  
```

**第三步：** 这一步比较重要，也是最值得深究的一步，也是容易犯迷糊的地方，仔细看。将定义的类注册到工厂类中。也就是说将类名称字符串和创建类实例的回调函数保存到工厂类的map中。这里我们又需要完成两个工作，第一个是定义一个创建类实例的回调函数，第二个就是将类名称字符串和我们定义的回调函数保存到工厂类的map中。假设我们定义了一个TestClassA。

```javascript
//test class A
class TestClassA{
public:
    void m_print(){
        cout<<"hello TestClassA"<<endl;
    };
};

//@brief:创建类实例的回调函数
TestClassA* createObjTestClassA{
        return new TestClassA;
}
```

好了，我们完了第一个工作，定义了一个创建类实例的回调函数。下面我们要思考一下如何将这个回调函数和对应的类名称字符串保存到工厂类的map中。我这里的一个做法是创建一个全局变量，在创建这个全局变量时，调用的构造函数内将回调函数和对应的类名称字符串保存到工厂类的map中。在这里，这个全局变量的类型我们定义为RegisterAction。

```javascript
//注册动作类
class RegisterAction{
public:
    RegisterAction(string className,PTRCreateObject ptrCreateFn){
        ClassFactory::getInstance().registClass(className,ptrCreateFn);
    }
};
```

有个这个注册动作类，我们在每个类定义完成之后，我们就创建一个全局的注册动作类的对象，通过注册动作类的构造函数将我们定义的类的名称和回调函数注册到工厂类的map中。可以在程序的任何一个源文件中创建注册动作类的对象，但是在这里，我们放在回调函数后面创建。后面你就知道为什么这么做了。创建一个注册动作类的对象如下：

```javascript
RegisterAction g_creatorRegisterTestClassA("TestClassA",(PTRCreateObject)createObjTestClassA);   
```

到这里，我们就完成将类名称和创建类实例的回调函数注册到工厂类的map。下面再以另外一个类TestClassB为例，重温一下上面的步骤：

```javascript
//test class B
class TestClassB{
public:
    void m_print(){
        cout<<"hello TestClassB"<<endl;
    };
};

//@brief:创建类实例的回调函数
TestClassB* createObjTestClassB{
        return new TestClassB;
}
//注册动作类的全局实例
RegisterAction g_creatorRegisterTestClassB("TestClassB",(PTRCreateObject)createObjTestClassB);
```

聪明的你，有没有发现，如果我们再定义一个类C、D….，我们重复的在写大量相似度极高的代码。那么我们如何偷懒呢，让代码变得简洁，提高我们的编码效率。有时我们就应该偷懒，不是说这个世界是懒人们创造的么，当然这些懒人们都很聪明。那么我们如何偷懒呢，如果你想到了宏，恭喜，答对了。其实仔细一看，包括回调函数的定义和注册动作的类的变量的定义，每个类的代码除了类名外其它都是一模一样的，那么我们就可以用下面的宏来替代这些重复代码。

```javascript
#define REGISTER(className)                                             \
    className* objectCreator##className(){                              \
        return new className;                                           \
    }                                                                   \
    RegisterAction g_creatorRegister##className(                        \
        #className,(PTRCreateObject)objectCreator##className)
```

有了上面的宏，我们就可以在每个类后面简单的写一个`REGISTER(ClassName)` 就完成了注册的功能，是不是很方便快捷呢！！！

## 2.3测试

至此，我们就完成了C++反射的部分功能，为什么是部分功能，后面再另外说明。急不可耐，我们先来测试一下，是否解决了上面我们提到的问题：**如何通过类的名称字符串来生成类的对象**。测试代码如下：

```javascript
#include <map>
#include <iostream>
#include <string>
using namespace std;

//test class
class TestClass{
public:
    void m_print(){
        cout<<"hello TestClass"<<endl;
    };
};
REGISTER(TestClass);

int main(int argc,char* argv[]){
    TestClass* ptrObj=(TestClass*)ClassFactory::getInstance().getClassByName("TestClass");
    ptrObj->m_print();
}
```

程序编译运行输出：  

![img](https://ask.qcloudimg.com/raw/yehe-4fad69835728/t1bc6poxi3.png?imageView2/2/w/1620)

## 2.4可能存在的疑问

看了上面的测试代码，大家可能会唏嘘不已，我们在通过类名称字符串创建类实例的时候，我们还是需要用到类名进行强制类型转换，有了类名称，我们何必还要处心积虑实现反射的功能呢，直接用类名创建实例不就行了么？

其实，上面实现的反射只是解决了本文最初提出的问题。那么在实际的项目中，还有一种应用场景就是我们定义好了基类，给客户继承，但是我们并不知道客户继承基类后的类型名称。我们可以通过配置文件说明客户实现的具体类型名称，这样我们就可以通过类名称字符串来创建客户自定义类的实例了。

# 3.还有其它的注册方法吗？

上面具体讲解了通过实现C++的反射来达到通过类名称字符串创建类的实例。其中，在对需要反射的类进行注册的时候，我们用到了一个注册动作类的全局变量，来辅助我们达到注册的功能。除了这个方法，还有没有别的方法呢？大家可以想一想。如有想法，也请留言告之。

仔细一想，我们通过全局对象的构造函数将类的创建实例的函数注册到工厂类中，其实我们是利用了全局对象的初始化执行的构造函数是在程序进入main函数之前执行的，这个问题就可以抽象为C/C++中如何在main()函数之前执行一条语句？

主要有以下几种方法：  （1）全局变量的构造函数。  也就是上面介绍的通过全局对象的构造函数来实现在main函数之前执行想要的操作。但是很明显的副作用就是定义了一个不从使用的全局变量，从出生，完成使命，就被我们无情的抛弃。

（2）全局变量的赋值函数。  跟上面的方法有异曲同工之妙，但也同样有着上面的副作用。参考如下代码：

```javascript
#include <iostream>
using namespace std;

int foo(void);
int i=foo();

int foo(void)
{
    cout<<"before main"<<endl;
    return 0;
}
int main(void)
{
    cout<<"i'm main"<<endl;
}

作者：Zenzen
链接：http://www.zhihu.com/question/26031933/answer/40612536
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

（3）使用GCC的话，可以通过attribute关键字声明constructor和destructor分别规定函数在main函数之前执行和之后执行。

```javascript
#include <stdio.h>  

__attribute((constructor)) void before_main()  
{  
    printf("%s/n",__FUNCTION__);  
}  

__attribute((destructor)) void after_main()  
{  
    printf("%s/n",__FUNCTION__);  
}  

int main( int argc, char ** argv )  
{  
    printf("%s/n",__FUNCTION__);  
    return 0;  
}  
```

（4）指定入口点，入口点中调用原来的入口点  在使用gcc编译C程序时，我们可以使用linker指定入口，使用编译选项-e指明程序入口函数。

```javascript
//test.c
#include<stdio.h>

int main(int argc, char **argv) {
    printf("main\n");

    return 0;
}

int xiao(int argc, char **argv) {
    printf("xiao\n");

    return main(argc, argv);
}

编译语句可以为:gcc -e xiao test.c

作者：萧井陌
链接：http://www.zhihu.com/question/26031933/answer/31872780
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

上面是知乎用户提出的方法，但是当我在测试的时候，运行到main函数中，总是会出现段错误。C++程序时，使用g++如法炮制，编译可以通过，也是执行到main函数时却是中抛出Segmentation fault (core dumped)。有兴趣的读者可以尝试一下，编译的时候记得给新的入口函数添加extern “C”说明，以防g++编译时改变了函数签名。 如果解决了，请留言告知。

（5）可以用main调用main实现在main前执行一段代码，如下：

```javascript
#include<stdio.h>
#include<stdbool.h>

int
main(int argc, char **argv) {
    static _Bool firstTime = true;
    if(firstTime) {
        firstTime = false;
        printf("BEFORE MAIN\n");
        return main(argc, argv);
    }

    printf("main\n");

    return 0;
}

作者：萧井陌
链接：http://www.zhihu.com/question/26031933/answer/31872780
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 4.小结

这里先解释一下上文中2.3节中提出的一个问题，我们为什么只是完成了C++反射的部分功能，因为我们在上面并没有完整的实现C++的反射机制，只能实现了反射机制中的一个小功能模块而已，即通过类名称字符串创建类的实例。除此之外，据我所知，编程语言的反射机制所能实现的功能还有通过类名称字符串获取类中属性和方法，修改属性和方法的访问权限等。

我们为什么需要反射机制。由于在 Java 和.NET 的成功应用，反射技术以其明确分离描述系统自身结构、行为的信息与系统所处理的信息，建立可动态操纵的因果关联以动态调整系统行为的良好特征，已经从理论和技术研究走向实用化，使得动态获取和调整系统行为具备了坚实的基础。当需要编写扩展性较强的代码、处理在程序设计时并不确定的对象时，反射机制会展示其威力，这样的场合主要有：  (1)序列化（Serialization）和数据绑定（Data Binding）。  (2)远程方法调用（Remote Method Invocation RMI）。  (3)对象/关系数据映射（O/R Mapping）。

当前许多流行的框架和工具，例如 Castor(基于 Java 的数据绑定工具)、Hibernate(基于 Java 的对象/关系映射框架)等，其核心都使用了反射机制来动态获得类型信息。因此，能够动态获取并操纵类型信息，已经成为现代软件的标志之一。 

反射机制如此复杂，C++尚不支持，岂是我这种三教九流之人的只言片语和几个代码片段所能够勾勒描绘的。

下面附上本文用到的完整代码，均写在一个源文件中，大家可以根据实际应用，将不同功能的代码写在不同的文件中。也可以在此基础上，进行功能扩充和改良。

```javascript
#include <map>
#include <iostream>
#include <string>
using namespace std;

typedef void* (*PTRCreateObject)(void);  

class ClassFactory{
private:  
    map<string, PTRCreateObject> m_classMap ;  
    ClassFactory(){}; //构造函数私有化

public:   
    void* getClassByName(string className);  
    void registClass(string name, PTRCreateObject method) ;  
    static ClassFactory& getInstance() ;  
};

void* ClassFactory::getClassByName(string className){  
    map<string, PTRCreateObject>::const_iterator iter;  
    iter = m_classMap.find(className) ;  
    if ( iter == m_classMap.end() )  
        return NULL ;  
    else  
        return iter->second() ;  
}  

void ClassFactory::registClass(string name, PTRCreateObject method){  
    m_classMap.insert(pair<string, PTRCreateObject>(name, method)) ;  
}  

ClassFactory& ClassFactory::getInstance(){
    static ClassFactory sLo_factory;  
    return sLo_factory ;  
}  

class RegisterAction{
public:
    RegisterAction(string className,PTRCreateObject ptrCreateFn){
        ClassFactory::getInstance().registClass(className,ptrCreateFn);
    }
};

#define REGISTER(className)                                             \
    className* objectCreator##className(){                              \
        return new className;                                           \
    }                                                                   \
    RegisterAction g_creatorRegister##className(                        \
        #className,(PTRCreateObject)objectCreator##className)

//test class
class TestClass{
public:
    void m_print(){
        cout<<"hello TestClass"<<endl;
    };
};
REGISTER(TestClass);

int main(int argc,char* argv[]){
    TestClass* ptrObj=(TestClass*)ClassFactory::getInstance().getClassByName("TestClass");
    ptrObj->m_print();
}
```

------

# 参考文献

1. [C++反射机制的实现](http://blog.csdn.net/cen616899547/article/details/9317323) 

2. [C++反射机制的一种简单实现..鲍亮, 陈平.计算机工程, 2006, 32(16):95-96](http://xueshu.baidu.com/s?wd=paperuri%3A(e42d3c2c287a001ce415545c2c48d755)&filter=sc_long_sign&tn=SE_xueshusource_2kduw22v&sc_vurl=http%3A%2F%2Fd.wanfangdata.com.cn%2FPeriodical%2Fjsjgc200616035&ie=utf-8&sc_us=13135807476108728082)  

3. [C/C++中如何在main()函数之前执行一条语句？](http://www.zhihu.com/question/26031933)

