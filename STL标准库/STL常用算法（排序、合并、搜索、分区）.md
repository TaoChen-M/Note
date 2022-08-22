# STL常用算法（排序、合并、搜索、分区）

## 排序函数

### sort()

本质上是一个模板函数该函数专门用来对容器或者普通数组中指定范围的元素进行排序。默认**升序**。基于**快排**实现。

#### 使用限制条件

- 容器支持的迭代器必须是随机访问迭代器，因此sort()只对array、vector、deque这三个容器提供支持
- 元素类型必须支持<小于运算符
- 实现排序时，需要交换容器中元素的存储位置，，这时如果容器中存储的是自定义的类对象，则该类内部必须提供移动构造函数和移动赋值运算符
- 对于指定区域内的值相等的元素，无法保证相对位置不发生改变
- 时间复杂度 $$N*log_2N$$,其中N为指定区域[first,last)之间的距离

#### 示例

```C++
bool fun(int i, int j) { return i > j; }

int main(){
    vector<int> vec{1, 2, 5, 7, 3, 7, 8};
	//默认使用升序 1 2 3 5 7 7 8
    sort(vec.begin(), vec.end());
    //使用自定义顺序 8 7 7 5 3 2 1
    sort(vec.begin(),vec.end(),fun);
    return 0;
}

```

### stable_sort()

当指定范围内有多个相等的元素时，stable_sort()可以保证位置不发生变化。有些场景需要保证相等元素的相对位置的，例如对于一个保存某种事务（比如银行账户）的容器，在处理这些事务之前，为了能够有序更新这些账户，需要按照账号对它们进行排序。而这时就很有可能出现相等的账号（即同一账号在某段时间做多次的存取钱操作），它们的相对顺序意味着添加到容器的时间顺序，此顺序不能修改，否则很可能出现账户透支的情况。

stable_sort()的用法和sort()函数完全相同，其时间复杂度是$$N*log_2N^2$$

当可用空间足够的时候，可以达到$$N*log_2N$$。基于归并排序实现。

### partial_sort()

部分排序，从指定区域中提取处部分数据，并对它们进行排序。partial_sort()函数会以交换元素存储位置的方式实现部分排序，具体来说，partial_sort()会将[first,last)范围内最小（最大）的middle-first个元素移动到[first,middle)区域中，并对这部分元素进行升序或者降序排序。

#### 示例

```c++
vector<int> vec{1, 2, 5, 4, 3, 7, 8};
partial_sort(vec.begin(),vec.begin()+4,vec.end());
for(auto it:vec){
    cout << it << endl;
}
```

### partial_sort_copy()

partial_sort_copy()和partial_sort()函数的区别在于，前者不会对原有数据做任何改动，而是先将选定的部分元素拷贝到另外指定的数组或者容器中，然后在对这部分元素进行排序。

```C++
int array[5] = {0};
vector<int> vec{1, 2, 5, 4, 3, 7, 8};
partial_sort_copy(vec.begin(),vec.end(),array,array+5);
for(auto it:array){
    cout << it << endl;
}
```

## find()

用来查找容器中的指定内容，传递给find的前两个参数是表示元素范围的迭代器，第三个参数是要查找的值。find将范围中的每个元素同给定值进行比较，返回指向第一个等于给定值的元素的**迭代器**，可以申明一个auto类型变量进行接收，如果找不到则会返回第二个传进的参数。

```C++
vector<int> vec{1,2,3,4,5};
auto find(vec.begin(),vec.end(),1);

string str("hello world")
str.find('h')//返回索引
find(str.begin(),str.end(),'h')//返回迭代器
```

类似的，由于指针就像内置数组上的迭代器，可以用find在数组中查找值

```c++
int val[]={1,2,3,4,5}
int *res=find(begin(val),end(val),1)
```

使用标准库中的begin和end函数来获得指向val中首元素和尾元素之后的指针，并传递给find。

## count()和count_if()

`count(begin,end,value)`，统计容器中等于value元素的个数，begin是容器的首迭代器，end是容器的尾迭代器，value是需要统计的元素。count函数的复杂度是线性的，最坏情况是O(n)。

`count_if(begin,end,value,cmp)`，cmp为比较的函数，返回值是bool类型。

```C++
vector<int> vec{1,2,3,4,5,6,7,8,9,0}

bool cmp(int x) return x%2;
count_if(vec.begin(),vec.end(),cmp);
```

## accumulate()

定义在头文件numeric中，该函数接收三个参数，前两个指出需要求和的元素的范围，第三个参数是和的初值。

```C++
//对vec中的元素进行求和，和的初值是0
int sum=accumulate(vec.cbegin(),vec.cend(),0)
```

