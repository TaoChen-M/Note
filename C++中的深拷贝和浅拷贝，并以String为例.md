# C++中的深拷贝和浅拷贝，并以String为例

### 浅拷贝

用一个类的对象去初始化另一个相同类的对象，如果在类中没有显示的定义拷贝构造函数，则编译器会生成一个默认的构造函数，这个构造函数通常是浅拷贝。

那么什么是浅拷贝呢？浅拷贝就是逐个字节的拷贝，如果是基本的数据类型，那么它是一份独立的拷贝，修改不同对象的值，不会相互影响；如果是引用数据类型，如对象、指针，那么他们是指向内存中的同一块地址。具体如下列代码所示：

```C++
class A{
public:
    int data=0;
    int* s;//s指向一个int型的指针数组
    A(int _data,int _s){
        data=_data;
        s=new int[_s];
        memset(s,0,_s*sizeof(int));//memset函数设置的值只能是0和-1两种整数类型或者字符，因为其是按照字节来进行赋值
    }
    ~A(){
        delete []s;
    }
};
int main(){
    A a(5,5),b(a);
    cout<<a.data<<endl;//5
    cout<<b.data<<endl;//5
    b.data=10;
    cout<<a.data<<endl;//5
    cout<<b.data<<endl;//10

    cout<<a.s[0]<<endl;//0
    cout<<b.s[0]<<endl;//0
    b.s[0]=100;
    cout<<a.s[0]<<endl;//100
    cout<<b.s[0]<<endl;//100
    
    //析构函数调用,先执行a.~A(),已经将内存中的数据释放掉了
    a.~A();
    b.~A();
}
```

浅拷贝会出现一个问题就是析构函数对同一块地址的重复释放，在上述代码中，如果我们显示的调用析构函数，就会发生错误，产生悬垂指针。

### 深拷贝

对于类中有指针这种情况，我们需要手动创建拷贝构造函数或者重载赋值运算符，深拷贝是将原先内存中的数据拷贝一份，放到新申请的内存中，并使用一个新的指针指向它。具体代码如下所示：

```C++
class String{
private :
    char* m_data;
public:
    //构造函数
    String(const char *str=0);
    //拷贝构造函数
    String(const String &str);
    //重载赋值
    String& operator=(const String& str);
    //析构函数
    ~String();
};
inline String::String(const char *str) {
    //如果使用值进行初始化，拷贝过来并增加一个\0表示字符串结束
    if (str){
        m_data=new char[strlen(str)+1];
        strcpy(m_data,str);
    }
    else{
        m_data=new char[1];
        *m_data='\0';
    }
}

inline String::String(const String &str) {
    m_data=new char[strlen(str.m_data)+1];
    strcpy(m_data,str.m_data);
}

//操作符重载一定是作用在左边的变量上
//类内操作符重载（作为成员函数）操作符一定是两个形参，但是在类内来写只有一个形参，this形参被省略
inline String &String::operator=(const String &str) {
    //如果是字符串本身直接等于即可
    if (this==&str){
        return *this;
    }
    else{
        delete[] str.m_data;
        m_data=new char[strlen(str.m_data)+1];
        strcpy(m_data,str.m_data);
        return *this;
    }
}

inline String::~String() {
    delete[] m_data;
}
```

