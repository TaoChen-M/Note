# list容器

list容器又称为双向链表容器，底层是以双向链表的形式实现的，元素分散存储在内存空间中。

## 创建list容器

1. 创建一个没有任何元素的空list容器

   ```C++
   list<int> values;
   ```

2. 创建一个包含n个元素的list容器

   ```c++
   list<int> values(10);
   ```

3. 创建一个具有n个元素的list，并为list指定值/

   ```C++ 
   list<int> values(10,5);
   ```

4. 已有list情况下，拷贝该容器，新旧容器的数据类型必须一致。

   ```C++
   list<int> values1(10);
   list<int> values2(values1);
   ```

5. 通过拷贝其他容器（或者普通数组）中指定区域内的元素，创建表新的list

   ```C++
   int a[]={1,2,3,4,5};
   list<int> values(a,a+5);
   
   array<int,5> arr{11,12,13,14,15};
   list<int> values(arr.begin()+2,arr.end());
   //拷贝容器中的{13，14，15}
   ```

   

## 访问list容器中的几种方法

list容器不支持随机访问，不提供下标操作符[ ]和at( )成员函数，也没有提供data( )成员函数。

通过front( )和back( )成员函数，分别获取list容器中的第一个元素和最后一个元素的引用。

```C++
    list<int> mylist{1,2,3,4,5};
    int &first = mylist.front();
    int end = mylist.back();

    first = 10;
    end = 50;

    cout << mylist.front() << endl;
    cout <<  mylist.back() << endl;
//结果：10、5
```

通过迭代器访问容器中的其他元素

```C++
const list<int> mylist{1,2,3,4,5};
auto i=mylist.begin();
while(i!=mylist.end()){
    cout<<*i<<endl;
    ++i;
}
```

## list中添加元素

- push_front()：向 list 容器首个元素前添加新元素；
- push_back()：向 list 容器最后一个元素后添加新元素；
- emplace_front()：在容器首个元素前直接生成新的元素；
- emplace_back()：在容器最后一个元素后直接生成新的元素；
- emplace()：在容器的指定位置直接生成新的元素；
- insert()：在指定位置插入新元素；
- splice()：将其他 list 容器存储的多个元素添加到当前 list 容器的指定位置处。

重点讲讲splice( )方法

| 语法格式                                                     | 功能                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| void splice(iterator pos,list& x)                            | position 为迭代器，用于指明插入位置；x 为另一个 list 容器。此格式的 splice() 方法的功能是，将 x 容器中存储的所有元素全部移动当前 list 容器中 position 指明的位置处。 |
| void splice(iterator pos,lisr& x,iterator i)                 | position 为迭代器，用于指明插入位置；x 为另一个 list 容器；i 也是一个迭代器，用于指向 x 容器中某个元素。此格式的 splice() 方法的功能是将 x 容器中 i 指向的元素移动到当前容器中 position 指明的位置处。 |
| void splice (iterator position, list& x, iterator first, iterator last); | position 为迭代器，用于指明插入位置；x 为另一个 list 容器；first 和 last 都是迭代器，[fist,last) 用于指定 x 容器中的某个区域。此格式的 splice() 方法的功能是将 x 容器 [first, last) 范围内所有的元素移动到当前容器 position 指明的位置处。 |

list容器底层使用的链表存储结构，splice( )成员方法移动元素的方式是，将存储元素的节点从旧list容器底层的链表中摘除，然后再接到新list容器底层的链表中。这意味着，该元素会从旧容器中删除

```C++
//创建并初始化 2 个 list 容器
list<int> mylist1{ 1,2,3,4 }, mylist2{10,20,30};
list<int>::iterator it = ++mylist1.begin(); 
//指向 mylist1 容器中的元素 2
   
//调用第一种语法格式
mylist1.splice(it, mylist2); 
// mylist1: 1 10 20 30 2 3 4
// mylist2:
// it 迭代器仍然指向元素 2，只不过容器变为了 mylist1

//调用第二种语法格式，将 it 指向的元素 2 移动mylist2.begin() 位置处
mylist2.splice(mylist2.begin(), mylist1, it);   
// mylist1: 1 10 20 30 3 4
// mylist2: 2
// it 仍然指向元素 2
   
//调用第三种语法格式，将[mylist1.begin(),mylist1.end())范围内的元素移动到 mylist.begin() 位置处                  
mylist2.splice(mylist2.begin(), mylist1, mylist1.begin(), mylist1.end());
//mylist1:                                                //mylist2:1 10 20 30 3 4 2
```

## 删除元素

| 成员函数    | 功能                                                         |
| ----------- | ------------------------------------------------------------ |
| pop_front() | 删除位于 list 容器头部的一个元素。                           |
| pop_back()  | 删除位于list容器尾部的一个元素                               |
| erase()     | 删除list容器中指定位置处的元素，也可以删除容器中某个区域的多个元素 |
| clear()     | 删除list中所有的元素                                         |
| remove(val) | 删除容器中所有等于val的元素                                  |
| unique()    | 删除容器中相邻的重复元素，只保留一份                         |
| remove_if() | 删除容器中满足条件的元素                                     |

## forward_list 容器

forward_list是C++ 11新添加的容器，底层实现和list一样，采用链表结构，只不过使用的是单链表，而list使用的是双链表。

单链表只支持从前向后遍历，不支持反向遍历，因此forward_list只有前向迭代器，无双向迭代器。存储相同个数的同类型元素，单链表耗用的内存空间更少，空间利用率更高，换句话说，只要是list容器和forward_list容器都能实现的操作，优先选择forward_list容器。