

acm 接收不定长的以空白符间隔的一行

```c++
#include<string>
#include<sstream>
int main ()
{
    std::string str;
    getline(cin,str);
    stringstream ss(str);
    int target;
    while (ss >> target)
    {
        // ar.push_back(target);
    }
}
```

