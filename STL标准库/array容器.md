# array容器

array容器是c++11中新增的序列容器，在普通数组的基础上，添加了成员函数和全局函数，使用上比普通数组更安全，效率未从变差。

array容器大小固定，无法动态扩展或收缩，只允许访问或者替换存储的元素。

使用之前，代码中引入*<array>*头文件，在*array<T,N>*类模板中，T用于指明容器中存储的具体数据类型，N用于指明容器的大小，**必须是常量**

```C++
#include<array>
using namespace std;

array<int,10> values;
//创建了一个具有10个int类型元素的array容器，此方式创建的容器各个元素值是不确定的（array容器不会做默认初始化操作）

array<int,10> values{};
//容器中所有的元素都会被初始化为0.0
```

## 访问array容器中的单个元素

1. 通过`容器名[ ]`的方式直接访问和使用容器中的元素，与c++标准库中访问数组的方式相同

   ```c++
   values[4]=values[3]+2.0*values[1]
   //即便使用越界的索引值去访问和存储元素也不会被检测到
   ```

2. 为了有效避免越界访问的情形，使用`array`容器的`at( )`成员函数

   ```c++
   values.at(4)=values.at(3)+2.0*values.at(1)
   //当传入索引是越界值时，程序会抛出out_of_range异常，推荐使用这种方法访问指定元素
   ```

3. `get(n)`模板函数，能够获取到容器的第n个元素

   ```C++
   array<string,5> words {"one","two","three","four","five"};
   cout<<get<3>(words)<<endl;
   cout<<get<6>(words)<<endl;//越界，会发生编译错误
   ```

4. `data()`,通过调用该函数可以得到指向容器首个元素的指针，通过该指针，可以获得容器中的各个元素

   ```C++
   array<int,5> values{1,2,3,4,5};
   cout<<*(values.data()+1)<<endl;//获取array中第二个元素
   ```

## 为什么说array容器是普通数组的升级版

同普通数组一样，C++标准库使用array容器存储的所有元素一定会位于连续且相邻的内存中。通过以下代码可以验证这一点：

```c++
#include<iostream>
#include<array>
using namespace std;

int main() {

	array<int, 10> values{ 3,5,6,67 };

	cout << &values[2] << endl;
	cout << &values[0] + 2 << endl;

	return 0;
}
输出结果：
010FF9A4
010FF9A4
```

用array容器替换普通数组的好处就是，array模板类中封装了大量的实用方法，例如完全可以用array容器去存储`char *`或者`const char *`l类型的字符串。

```c++
#include<iostream>
#include<array>
using namespace std;

int main() {
	array<char, 50> str{ 1,2,3 };

	strcpy(&str[0], "hello,world!");

	printf("%s", &str[0]);

	return 0;
}

其中代码中的&str[0]可以用str.data()来替代
    strcpy(a.data(), "http://c.biancheng.net/stl");
    printf("%s", a.data());
注意容器中的迭代器和指针是不能混用的，即上面的代码中不能用str.begin()来替代&str[0]或者str.data()
```

