# stack栈容器

栈是一种先入后出的容器，增加元素叫压栈或者入栈；移除元素叫做出栈。

## stack定义

```C++
stack<int> mystack;
//在创建堆栈时，不能够直接进行初始化，但是可以用别的容器进行初始化
deque<int> deq{1,2,3,4};
stack<int> mystack(deq);
```

stack容器适配器模板有两个参数：

第一个参数是存储对象的类型；

第二个参数是底层容器的类型。默认底层容器是deque<T>容器，标准容器模板vector、deque和list都满足这些要求，因此可以自己指定stack底层存储的类。

```C++
stack<typename T,typename Container=deque<t>>
```

## 访问元素

- top()输出栈顶元素，并不能删除栈顶元素
- empty()判断栈是否为空
- size()容器元素个数

## 入栈和出栈

- 入栈：push()
- 出栈：pop()

## 判断栈是否相等

- 直接用==符号即可