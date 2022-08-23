# C++中的sort自定义排序

## 使用外部排序函数

```C++
bool cmp(int x,int y){
    return x>y;
}
int main(){
    vector<int> t{1,3,5,2};
    sort(t.begin(),t.end());//1,2,3,5 默认的是增序排序
    sort(t.rbegin(),t.rend());//降序排序
    sort(t.begin(),t.end(),cmp);//5,3,2,1 因为进行比较的时候当前位置X>y返回的是true，所以是降序排序 
}
```

## lambda表示法

```C++
int main(){
	vector<int> t{1,3,5,2};
    sort(t.begin(),t.end(),[](int x,int y){return x>y;});//5 3 2 1
    
	//如果说这里使用了外部相关变量，比如说是对map中的val进行比较，可以在[]中使用&符号 
	unordered_map<int,int> tmp{{1,5},{2,7},{3,1},{5,0}};
	sort(t.begin(),t.end(),[&](int x,int y){return tmp[x]>tmp[y];});
}
```

