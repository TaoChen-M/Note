# vector容器

vector容器通常被称为向量容器，因为该容器擅长在尾部插入或删除元素，在常量时间内就可以完成，时间复杂度是O(1)；对于在容器头部或者中部插入或删除元素，则花费时间较长，时间复杂度是O(n)。

*vector*和*array*非常相像，都是对普通数组的升级版，但是*array*实现的是静态数组，*vector*是一个动态数组，即可以进行元素的插入和删除，*vector*会动态的调整所占用的内存空间。

## 创建vector的几种方式

`vector<double> values;`

这是一个空的vector容器，因为容器中没有元素，所以没有为其分配内存空间，当添加第一个元素（比如使用push_back()函数），vector会自动分配内存

`vector<double> values(20);`

还可以像下面这样通过调用 reserve() 成员函数来增加容器的容量

`values.reserve(20);`

注意，如果 vector 的容量在执行此语句之前，已经大于或等于 20 个元素，那么这条语句什么也不做；另外，调用 reserve() 不会影响已存储的元素，也不会生成任何元素，即 values 容器内此时仍然没有任何元素。还需注意的是，如果调用 reserve() 来增加容器容量，之前创建好的任何迭代器（例如开始迭代器和结束迭代器）都可能会失效，这是因为，为了增加容器的容量，vector<T> 容器的元素可能已经被复制或移到了新的内存地址。所以后续再使用这些迭代器时，最好重新生成一下。

通过存储元素类型相同的其他*vector*容器，也可以创建新的*vector*容器：

```c++
vector<char> value1(5,'c')//指定元素的初始值，有5个字符c
vector<char> value2(value1)//value2中也有了5个字符c
    
在此基础上可以使用一对指针或者迭代器来指定初始值的范围
int array[]={1,2,3};
std::vector<int>values(array, array+2);//values 将保存{1,2}
std::vector<int>value1{1,2,3,4,5};
std::vector<int>value2(std::begin(value1),std::begin(value1)+3);//value2保存{1,2,3}
```

## 访问元素

- [ ]方式，如果越界或出现其他错误，不会抛出异常，可能会崩溃

- at()方式，如果越界或出现其他错误，会抛出异常

- 迭代器遍历，同时迭代器提供了逆向遍历；

  正向：`vector.begin(),vector.end()`

  逆向：`vector.rbegin(),vector.rend()`

- `front()`和`back()`返回容器的第一个和最后一个元素的引用

- `data()`成员函数，返回指向容器中首个元素的指针，通过此指针可以修改容器中的元素

```c++
#include <vector>
int main()
{
    vector<int> values{1,2,3,4,5};
    
    //[]访问，修改容器中下标为 0 的元素的值
    values[0] = values[1] + values[2] + values[3] + values[4];
    
	//at()方法
    values.at(0) = values.at(1) + values.at(2) + values.at(3) + values.at(4);
    
    //front()和back()方法，修改首尾元素
    values.front() = 10;
    values.back() = 20;
    
    //data[]方法、修改容器中第 2 个元素的值
    *(values.data() + 1) = 10;
    
     //迭代器遍历
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
        cout << *it << " ";
    }
    
    //迭代器逆向遍历
    for (vector<int>::reverse_iterator it = v1.rbegin(); it != v1.rend(); it++) {
        cout << *it << " ";
    }
    
    return 0;
}
```

## 添加元素

向*vector*中添加元素的唯一方式就是使用它的成员函数，迭代器显然不行。

- `push_back()和emplace_back()`在尾部添加元素
- `insert()`函数，结合迭代器位置插入指定元素

*push_back( )*和*emplace_back( )*

```c++
#include <vector>
int main()
{
    vector<int> values{};  
    //向容器中添加元素1和2
    values.push_back(1);
    values.emplace_back(2);
    return 0;
}
```

*emplace_back( )*和*push_back( )*的区别

在于底层实现的机制不同。push_back() 向容器尾部添加元素时，首先会创建这个元素，然后再将这个元素拷贝或者移动到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）；而 emplace_back() 在实现时，则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程。

换句话说，emplace_back() 的执行效率比 push_back() 高。因此，在实际使用时，建议大家优先选用 emplace_back()。

*insert( )*是向容器中指定位置处插入一个或者多个元素。

| 语法格式               | 用法说明                                                     |
| ---------------------- | ------------------------------------------------------------ |
| insert(pos,elem)       | 迭代器pos指定位置插入一个新元素elem                          |
| insert(pos,n,elem)     | 迭代器pos指定位置插入n个元素elem                             |
| insert(pos,first,last) | 迭代器pos指定位置插入其他容器（不仅限于vector）中位于[first,end)区域的元素 |
| insert(pos,initlist)   | 迭代器pos指定位置，插入初始化列表（大括号{}括起来的多个元素，中间有逗号隔开）中的所有元素 |

```C++ 
#include <vector> 
#include <array> 
int main()
{
    vector<int> demo{1,2};
    //指定位置插入一个元素
    demo.insert(demo.begin() + 1, 3);//{1,3,2}
    
    //指定位置插入多个元素
    demo.insert(demo.end(), 2, 5);//{1,3,2,5,5}
    
    //指定位置插入其他容器[first,last)区域内的元素
    array<int,3>test{ 7,8,9 };
    demo.insert(demo.end(), test.begin(), test.end());//{1,3,2,5,5,7,8,9}
    
    //指定位置插入初始化列表
    demo.insert(demo.end(), { 10,11 });//{1,3,2,5,5,7,8,9,10,11}
    
    return 0;
}
```

## 删除元素

- pop_back()，移除尾部元素
- erase()，擦除迭代器指定位置元素
- remove()，删除所有和指定元素相等的元素

| 函数           | 功能                                                         |
| -------------- | ------------------------------------------------------------ |
| pop_back( )    | 删除容器中最后一个元素，容器的大小（size）减1，容量（capacity）不发生改变 |
| erase( pos)    | 删除容器中pos迭代器指定位置的元素，并返回指向被删除元素下一个位置的元素的迭代器，size-1；capacity不变 |
| erase(beg,end) | 删除位于[begin,end)之间的所有元素，返回指向被删除区域下一个位置元素的迭代器，size减小，capacity不变 |
| remove( )      | 删除所有和指定元素值相等的元素，并指向最后一个元素下一个位置的迭代器，size和capacity不变 |
| clear( )       | 是容器变为空，使size变成0，但capacity不变                    |

```C++
#include <vector>
int main()
{
    vector<int>demo{ 1,2,3,4,5 };
    //dmeo 容器新的size   4
    //demo 容器新的容量   5
    demo.pop_back();
	
    auto iter = demo.erase(demo.begin() + 1);//删除元素 2
    //iter迭代器指向元素 3
    cout << endl << *iter << endl;
 
    return 0;
}
```

## 如何避免vector容器进行不必要的扩容

vector扩容的整个过程如下4个步骤：

1. 分配一块大小是当前vector容量几倍的新存储空间（STL版本中通常都是以2的倍数增长，即每次扩容容量都是之前的2倍）
2. 将vector容器存储的所有元素，依照原有次序从旧的空间中复制到新的空间中。
3. 析构掉旧存储空间中存储的所有元素。
4. 释放掉旧的存储空间。

| 成员方法    | 功能                                                         |
| ----------- | ------------------------------------------------------------ |
| size( )     | 告诉我们当前容器中已经存在有多少个元素，无法得知容器有多少存储空间 |
| capacity( ) | 获取容器总共可以容纳多少个元素，，可以通过capacity( )-size( )得知容器中有多少未被使用的存储空间 |
| resize( )   | 强制容器必须存储n个元素，如果n比size( )返回值小，则容器尾部多出的元素将会被析构（删除），如果n比size( )大，则vector会借助默认构造函数创建更多的默认值元素，并将他们存储在容器末尾；如果n比capacity( )的返回值还要大，则vector先会扩增，在添加默认值元素。 |
| reserve( )  | 强制vector的容量至少是n，如果n比当前vector的容量小，则该方法什么也不做，反之如果n比容量大，vector先扩容。 |

通过对以上几个成员方法功能的分析，我们可以总结出一点，即**只要有新元素要添加到 vector 容器中而恰好此时 vector 容器的容量不足时，该容器就会自动扩容。**

避免 vector 容器执行不必要的扩容操作的关键在于，在使用 vector 容器初期，就要**将其容量设为足够大的值**。换句话说，**在 vector 容器刚刚构造出来的那一刻，就应该借助 reserve() 成员方法为其扩充足够大的容量。**

假设我们想创建一个包含 1~1000 的 vector<int>，通常会这样实现：

```c++
vector<int> vec;
for (int i;i<=1000;++i){
    vec.push_back(i);
}
```

在上面代码中，vector容器会自动进行2-10次的扩容，1000约为2的10次方，程序的执行效率非常之低。

下面的代码演如何使用reserve( )成员方法尽量避免vector容器执行不必要的操作。

```C++
vector<int> vec;
vec.reserve(1000);
for (int i=1;i<=1000;++i){
    vec.push_back(i);
}
```

相比之前的代码，此次vector容器仅进行了一次扩容，效率大大增加。