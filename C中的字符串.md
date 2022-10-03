# C中的字符串

C中没有显示的字符串变量，使用字符串指针或者字符串数组的形式

## 字符串长度

`strlen`函数，返回字符串中的字符个数，返回值类型是`size_t`，如果使用`strlen`比较两个字符串的长度，不要使用相减的方式，`size_t`相减的结果是一直大于0的

## 复制字符串

`strcpy(char *dst, const char *src)`，如果源字符串的长度小于目标字符串的长度，复制过来的时候源字符串的`\0`也会被复制过来，那么在目标`dst`中`\0`之后的内容将会被有效的删除。

如果源字符串的长度大于目标字符串的长度，`strcpy`函数会侵占数组后面的内存空间，可能会导致原先存在那里的变量的丢失。

## 连接字符串

`char strcat(char *dst,const char * src)`，将一个字符串连接到目标字符串的后面，目标字符串可以是空字符串。

## 字符串比较函数

`int strcmp(char const *s1, char const *s2)`，按照字典序进行比较，如果`s1<s2`，会返回一个负值，相等是0，大于返回正值。

## 字符判定操作

|     函数     |     作用     |
| :----------: | :----------: |
| `isspace()`  |     空格     |
| `isdigit()`  |     数字     |
| `isxdigit()` | 十六进制数字 |
| `islower()`  |   小写字母   |
| `isupper()`  |   大写字母   |
| `isalpha()`  |     字母     |
| `isalnum()`  | 字母或者数字 |

## 字符转换

|         函数          |   作用   |
| :-------------------: | :------: |
| `int tolower(int ch)` | 转为小写 |
| `int toupper(int ch)` | 转为大写 |

## 内存操作函数

```C++
void *memcpy(void *dst,void const *src,size_t length);//内存拷贝
void *memset(void *a,int ch,size_t length);//内存赋值，一般都是赋值为char，int型也可以但是只能是0或者-1
```

