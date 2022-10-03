# C++中的结构体内存对齐 

首先来看一下常见的数据类型占用的内存大小：

![](C:\Users\Tchen\Desktop\研究生\Note\images\数据成员大小.png)

对于基本数据类型32位主机和64位主机是相同的，不同的只有指针的大小。

结构体内存对齐的原因是可以提高内存的访问速度。

结构体内存对齐遵循两个规则：

- 数组成员对齐规则：首个元素放在偏移地址是0的位置，以后每个元素的偏移地址遵循`min(当前变量的字节大小，#pragma pack(n))`整数倍开始的地方，在windows中`#pragma pack`是8，在Linux中是4。
- 结构体大小（sizeof）规则：`min(数据成员最大的那个所占字节数，#pragma pack(n))`的整数倍
- 结构体作为成员的对齐规则：如果结构体B中嵌套了结构体A，那么还是以A中的最大数据成员大小来进行对齐，`min(结构体A中数据成员最大的那个所占字节数，#pragma pack(n))`。比如结构体A中有char、int、double等成员，那么B中A的偏移地址应该从8的整数倍开始存储。

参考示例：

```C++
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stddef.h>

#pragma pack(4)   // 16 8 4 2 1

struct Student
{
    int a;
    char b;
    double c;
    float d;
};

struct Student2
{
    char a;
    struct Student b;
    double c;
};

void test()
{
    printf("#pragma pack(4)\n");
    printf("%d\n", offsetof(struct Student, a));
    printf("%d\n", offsetof(struct Student, b));
    printf("%d\n", offsetof(struct Student, c));

    printf("%d",sizeof(struct Student));
}
int main(){
    test();
//输出:
#pragma pack(4)
0
4
8
20
```

