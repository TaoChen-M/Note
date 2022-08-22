# string标准库

标准库string表示可变长的字符序列，使用string类型必须先包含string头文件。

```C++ 
#include<string>
```

## 定义和初始化string对象

1. 初始化一个空的string对象。

   ```C++ 
   string s;
   //默认初始化，s是一个空串
   ```

2. 使用已有string对象来初始化。

   ```C++
   string s1;
   string s2(s1);
   string s3=s1;
   //s3和s2是s1的副本，其中s3和s2的定义方式相同
   ```

3. 初始化为由连续n个字符组成的串

   ```C++
   string s(n,'c');
   //s由n个字符c组成
   ```

4. 使用指定字符串初始化。

   ```C++ 
   string s("values");//直接初始化
   string s="values";//拷贝初始化
   string s{"this is a string"}
   ```

## string对象操作函数

| 成员函数      | 功能说明                                             |
| ------------- | ---------------------------------------------------- |
| getline(is,s) | 从is中读取一行赋给s，返回is                          |
| s.empty()     | s为空返回true，否则返回false                         |
| s.size()      | 返回s中字符的个数                                    |
| s[n]          | 返回s中第n个字符的引用                               |
| s1+s2         | 返回s1和s2连接后的结果                               |
| s1=s2         | 用s2的副本代替s1中原来的字符                         |
| s1==s2        | 如果s1和s2中所包含的字符完全一样，则相等             |
| s1！=s2       | 相等性判断对大小写敏感                               |
| <,<=,>,>=     | 利用字符在字典中的顺序进行比较，且对字母的大小写敏感 |

### size()函数细讲

size函数返回的是一个size_type类型的值，这是一个无符号整型值，能够存放下任意的大小，如果一个表达式中已经有了size()函数，就不要使用int，这样可以避免混用int和unsigned带来的问题。

## 字符串的遍历

三种方式

- 数组方式遍历，通过[]操作符遍历（不会抛出异常）
- at()方法遍历，根据index取值（会抛出异常）
- 通过STL迭代器遍历

```C++
//数组方式遍历
for(int i=0;i<str.length();++i){
    cout<<str[i];
}
//at方法遍历
for(int i=0;i<str.length();++i){
    cout<<str.at(i);
}
//迭代器遍历
for(string::iterator it=str.begin();it!=str.end();++it){
    cout<<*it;
}
```



## string的拼接

当把string对象和字符以及字符串混在一个语句中的时候，必须确保每个加号的两侧运算对象至少有一个是string。

```c++
string s2=s1+",";//正确，把一个string对象和一个字符相加
string s3="hello"+",";//错误：两个运算对象都不是string
string s4=s1+","+"hello";//正确：相当于(s1+",")+"hello"括号中的结果相当于是一个string对象
string s5="hello"+","+s1;//错误：等价于("hello"+",")+s1,括号内试图将两个字符串进行相加
```

### 练习题

编写一段程序从标准输入中一次输入一整行，然后修改程序使其一次读入一个单词。

```C++
//一次输入一整行
int main(){
    string s;
    while(getline(cin,s)){
        cout<<s<<endl;
    }
    return 0;
}

//一次读入一个单词
int main(){
    string s;
    while(cin>>s){
        cout<<s<<endl;
    }
}
```

说明string类的输入运算符和getline函数分别是如何处理空白字符的。

- 类似`is>>s`的读取：string对象会忽略开头的空白并从第一个真正的字符开始，直到遇见下一**空白**为止
- 类似`getline(is, s)`的读取：string对象会从输入流中读取字符，直到遇见**换行符**为止

编写一段程序读取两个字符串，比较其是否相等并输出结果。如果不相等，输出比较大的那个字符串。改写上述程序，比较输入的两个字符串是否等长，如果不等长，输出长度较大的那个字符串。

比较大的

```c++
int main()
{
	string str1, str2;
	while (cin >> str1 >> str2)
	{
		if (str1 == str2)
			cout << "The two strings are equal." << endl;
		else
			cout << "The larger string is " << ((str1 > str2) ? str1 : str2);
	}

	return 0;
}
```

比较长度

```C++
int main()
{
	string str1, str2;
	while (cin >> str1 >> str2)
	{
		if (str1.size() == str2.size())
			cout << "The two strings have the same length." << endl;
		else
			cout << "The longer string is " << ((str1.size() > str2.size()) ? str1 : str2) << endl;
	}

	return 0;
}
```

编写一段程序从标准输入中读入多个字符串并将他们连接起来，输出连接成的大字符串。然后修改上述程序，用空格把输入的多个字符串分割开来。

```C++ 
//未隔开的
string result, s;
while (cin >> s)
{
	result += s;
}
//隔开的
string result, s;
while (cin >> s)
{
	result += s + " ";
}
cout << result << endl;
```

### 处理string对象中的字符

如果想对string对象中的每个字符操作，最好的操作语句是：*范围 for(range for)* ，这种语句遍历给定序列中的每个元素并对每个值执行某种操作。简单例子，使用for语句把string对象中的字符每行一个打印出来：

```C++
string str("some string");
for(auto c:str)
    cout<<c<<endl;

//通过设置循环变量为引用类型，可以修改元素的值
for(auto &c:str)
    c=toupper(c);
```

cctype头文件中的函数

| 函数        | 功能                                              |
| ----------- | ------------------------------------------------- |
| isalnum(c)  | 当c是字母或数字时为真                             |
| isalpha(c)  | 当c是字母时为真                                   |
| iscntrl(c)  | 当c是控制字符时为真                               |
| isdigit(c)  | 当c是数字时为真                                   |
| isgraph(c)  | 当c不是空格但可以打印时为真                       |
| islower(c)  | 当c是小写字母时为真                               |
| isprint(c)  | 当c是可打印字符时为真（即c是空格或c具有可视形式） |
| ispunct(c)  | 当c是标点符号时为真                               |
| isspace(c)  | 当c是空格时为真                                   |
| isupper(c)  | 当c是大写字母时为真                               |
| isxdigit(c) | 当c是十六进制数字时为真                           |
| tolower(c)  | 如果c是大写字母，输出对应的小写字母；否则原样输出 |
| toupper(c)  | 如果c是小写字母，输出对应的大写字母；否则原样输出 |

### decltype类型推导

当需要定义变量时，但不知变量的类型，防止因类型不同导致的错误，可使用decltype，auto 所修饰的变量必须被初始化，编译器需要通过初始化来确定 auto 所代表的类型，即必须要定义变量。若仅希望得到类型，而不需要（或不能）定义变量的时候使用decltype。

如根据string对象的size( )函数定义遍历的索引：

```C++
decltype(s.size()) index=0;
```

### 练习题

编写一段程序，使用范围for语句将字符串内的所有字符使用X代替

```C++
string s="this is a string";
for(auto &x:s)
    x='X';
cout<<s<<endl;
```

就上一题完成的程序而言，如果将循环控制的变量设置为char将发生什么？先估计一下结果，然后实际编程进行验证。

答：如果设置为char，那么原来的字符串不会发生改变。

注：但是在代码中将auto更改为char，仍然可以修改为"x"

分别用while循环和传统for循环重写第一题的程序，你觉得哪种形式更好呢？为什么？

```c++
decltype(s.size()) i = 0;
	while (i != s.size())
	{
		s[i] = 'X';
		++i;
	}
	cout << s << endl;
	for (i = 0; i != s.size(); ++i)
	{
		s[i] = 'Y';
	}
	cout << s << endl;
```

范围for语句更好，不直接操作索引，更简洁。

下面的程序有何作用？它合法吗？如果不合法？为什么？

```c++
string s;
cout << s[0] << endl;
```

答：不合法。使用下标访问空字符串是非法的行为。

编写一段程序，读入一个包含标点符号的字符串，将标点符号去除后输出字符串剩余的部分。

```C++
string s = "this, is. a :string!";
	string result;

	for (auto x : s)
	{
		if (!ispunct(x))
		{
			result += x;
		}
	}
	
	cout << result << endl;
```

下面的范围for语句合法吗？如果合法，c的类型是什么？

```C++
const string s = "Keep out!";
for(auto &c : s){ /* ... */ }
```

答：要根据for循环中的代码来看是否合法，c是string 对象中字符的引用，s是常量。因此如果for循环中的代码重新给c赋值就会非法，如果不改变c的值，那么合法。

### substr操作

substr操作返回一个string，是原始string的一部分或者全部的拷贝。

```C++
s.substr(pos,n)返回一个string，包含s中从pos开始的n个字符的拷贝。pos的默认值是0。n的默认值为s.size()-pos,即拷贝从pos开始的所有字符。
```

### 练习题

编写程序，从一个`vector<char>`初始化一个`string`。

```C++
vector<char> v{'h','e','l','l','o'};
string str(v.cbegin(),v.cend())
```

假定你希望每次读取一个字符存入一个`string`中，而且知道最少需要读取100个字符，应该如何提高程序的性能？

答：使用 `reserve(100)` 函数预先分配100个元素的空间。

## 删除和插入

| 函数               | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| s.insert(pos,args) | 在pos位置插入args指定的字符。pos可以是一个下标或者迭代器。如s.insert(s.size(),5,'!')//在s的末尾插入5个感叹号。 |
| s.erase(pos,len)   | 删除从位置pos开始的len个字符，如果len被省略则删除从pos开始至s末尾的所有字符。如s.erase(s.size()-5,5)//删除s的最后5个字符 |
| s.assign(args)     | 将s中的字符替换为args指定的字符                              |
| s.append(args)     | 将args追加到s，args只能是string对象                          |

### 练习题

编写一个函数，接受三个`string`参数是`s`、`oldVal` 和`newVal`。使用迭代器及`insert`和`erase`函数将`s`中所有`oldVal`替换为`newVal`。测试你的程序，用它替换通用的简写形式，如，将"tho"替换为"though",将"thru"替换为"through"。

```C++
auto replace_with(string &s, string const& oldVal, string const& newVal)
{
    for (auto cur = s.begin(); cur <= s.end() - oldVal.size(); )
        if (oldVal == string{ cur, cur + oldVal.size() })
            cur = s.erase(cur, cur + oldVal.size()),
            cur = s.insert(cur, newVal.begin(), newVal.end()),
            cur += newVal.size();
        else  
            ++cur;
}
//注意在erase和insert之后，重新指定迭代器cur的位置
```

重写上一题的函数，这次使用一个下标和`replace`

```C++
auto chaStr(string &s, string const& oldVal, string const& newVal) {
	for (decltype(s.size()) pos = 0; pos <= s.size();)
	{
		if(s[pos]==oldVal[0]&&s.substr(pos,oldVal.size())==oldVal){
			s.replace(pos, oldVal.size(), newVal);
			pos += newVal.size();
		}
		++pos;
	}
}
```

编写一个函数，接受一个表示名字的`string`参数和两个分别表示前缀（如"Mr."或"Mrs."）和后缀（如"Jr."或"III"）的字符串。使用迭代器及`insert`和`append`函数将前缀和后缀添加到给定的名字中，将生成的新`string`返回。

```c++
auto add_pre_and_suffix(string name, string const& pre, string const& su)
{
    name.insert(name.begin(), pre.cbegin(), pre.cend());
    return name.append(su);
}
```

重写上一题的函数，这次使用位置和长度来管理`string`，并只使用`insert`

```c++
auto add_pre_and_suffix(std::string name, std::string const& pre, std::string const& su)
{
    name.insert(0, pre);
    name.insert(name.size(), su);
    return name;
}
```

## string 中的查找和替换

| 函数名                    | 功能                                                         |
| ------------------------- | ------------------------------------------------------------ |
| s.find(args)              | 查找s中args第一次出现的位置，返回第一个匹配字母的下标        |
| s.rfind(args)             | 查找s中args最后一次出现的位置                                |
| s.find_first_of(args)     | 在s中查找args中任何一个字符第一次出现的位置                  |
| s.find_last_of(args)      | 在s中查找args中任何一个字符最后一次出现的位置                |
| s.find_first_not_of(args) | 在s中查找第一个不在args中的字符                              |
| s.find_last_not_of(args)  | 在s中查找最后一个不在args中的字符                            |
| s.replace(range,args)     | 删除s中范围range内的字符，替换为args指定的字符。range或者是一个下标和一个长度，或者是一对指向s的迭代器。如s.replace(11,3,"Fifth")//从位置11开始，删除3个字符并插入Fifth。 |

### 练习题

编写程序，首先查找`string`"ab2c3d7R4E6"中每个数字字符，然后查找其中每个字母字符。编写两个版本的程序，第一个要使用`find_first_of`，第二个要使用`find_first_not_of`。

```C++
string numbers("0123456789");
string s("ab2c3d7R4E6");

cout << "numeric characters: ";
for (int pos = 0; (pos = s.find_first_of(numbers, pos)) != string::npos; ++pos)
{
	cout << s[pos] << " ";
}

cout << "\nalphabetic characters: ";
for (int pos = 0; (pos = s.find_first_not_of(numbers, pos)) != string::npos; ++pos)
{
	cout << s[pos] << " ";
}
```

### compare函数

string类型的compare函数和c标准库的strcmp函数很相似，根据s是等于、大于还是小于参数指定的字符串，s.compare返回0、正数或者负数。

### 数值转换

| 函数           | 功能                                                         |
| -------------- | ------------------------------------------------------------ |
| to_string(val) | 返回数值val的string表示                                      |
| stof(s,p)      | 返回s的起始子串（表示浮点数）的数值，返回值类型是float、double |
| stod(s,p)      |                                                              |

### 练习题

编写程序处理一个`vector<string>`，其元素都表示整型值。计算`vector`中所有元素之和。修改程序，使之计算表示浮点值的`string`之和。

```C++
auto sum_for_int(std::vector<std::string> const& v)
{
    int sum = 0;
    for (auto const& s : v)
        sum += std::stoi(s);
    return sum;
}

auto sum_for_float(std::vector<std::string> const& v)
{
    float sum = 0.0;
    for (auto const& s : v)
        sum += std::stof(s);
    return sum;
}

std::vector<std::string> v = { "1", "2", "3", "4.5" };
```

设计一个类，它有三个`unsigned`成员，分别表示年、月和日。为其编写构造函数，接受一个表示日期的`string`参数。你的构造函数应该能处理不同的数据格式，如January 1,1900、1/1/1990、Jan 1 1900 等。

```C++
class My_date{
private:
    unsigned year, month, day;
public:
    My_date(const string &s){

        unsigned tag;
        unsigned format;
        format = tag = 0;

        // 1/1/1900
        if(s.find_first_of("/")!= string :: npos)
        {
            format = 0x01;
        }

        // January 1, 1900 or Jan 1, 1900
        if((s.find_first_of(',') >= 4) && s.find_first_of(',')!= string :: npos){
            format = 0x10;
        }
        else{ // Jan 1 1900
            if(s.find_first_of(' ') >= 3
                && s.find_first_of(' ')!= string :: npos){
                format = 0x10;
                tag = 1;
            }
        }

        switch(format){

        case 0x01:
            day = stoi(s.substr(0, s.find_first_of("/")));
            month = stoi(s.substr(s.find_first_of("/") + 1, s.find_last_of("/")- s.find_first_of("/")));
            year = stoi(s.substr(s.find_last_of("/") + 1, 4));

        break;

        case 0x10:
            if( s.find("Jan") < s.size() )  month = 1;
            if( s.find("Feb") < s.size() )  month = 2;
            if( s.find("Mar") < s.size() )  month = 3;
            if( s.find("Apr") < s.size() )  month = 4;
            if( s.find("May") < s.size() )  month = 5;
            if( s.find("Jun") < s.size() )  month = 6;
            if( s.find("Jul") < s.size() )  month = 7;
            if( s.find("Aug") < s.size() )  month = 8;
            if( s.find("Sep") < s.size() )  month = 9;
            if( s.find("Oct") < s.size() )  month =10;
            if( s.find("Nov") < s.size() )  month =11;
            if( s.find("Dec") < s.size() )  month =12;

            char chr = ',';
            if(tag == 1){
                chr = ' ';
            }
            day = stoi(s.substr(s.find_first_of("123456789"), s.find_first_of(chr) - s.find_first_of("123456789")));

            year = stoi(s.substr(s.find_last_of(' ') + 1, 4));
            break;
        }
    }

    void print(){
        cout << "day:" << day << " " << "month: " << month << " " << "year: " << year;
    }
};
int main()
{
    My_date d("Jan 1 1900");
    d.print();
    return 0;
}
```

使用`stack`处理括号化的表达式。当你看到一个左括号，将其记录下来。当你在一个左括号之后看到一个右括号，从`stack`中`pop`对象，直至遇到左括号，将左括号也一起弹出栈。然后将一个值（括号内的运算结果）`push`到栈中，表示一个括号化的（子）表达式已经处理完毕，被其运算结果所替代。

```C++
#include <stack>
#include <string>
#include <iostream>

using std::string; using std::cout; using std::endl; using std::stack;

int main()
{
    string expression{ "This is (pezy)." };
    bool bSeen = false;
    stack<char> stk;
    for (const auto &s : expression)
    {
        if (s == '(') { bSeen = true; continue; }
        else if (s == ')') bSeen = false;
        
        if (bSeen) stk.push(s);
    }
    
    string repstr;
    while (!stk.empty())
    {
        repstr += stk.top();
        stk.pop();
    }
    
    expression.replace(expression.find("(")+1, repstr.size(), repstr);
    
    cout << expression << endl;
    
    return 0;
}
```



