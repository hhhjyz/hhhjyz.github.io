- stack  local vars
- heap dynamically 
- code/data

**Global vars**

在任意函数外定义，`extern`声明说明其被其他编译单元定义的

**Static**

如果全局变量被声明为`static`，则其作用域仅限于定义它的文件，其他文件无法访问


**Static Local vars**

存储永久性

限制的访问范围


## Defining references

```cpp
type& refname = name ;
```
引用是变量的别名，`refname`和`name`指向同一个内存空间

引用在声明时必须初始化，且不能重新绑定到其他变量。

作为函数参数进行调用时可以不用初始化

**Type restrictions** 

No pointer to reference,but reference to pointer is ok
```cpp
int&* p; // error
void f(int*& p); // ok
```


## 动态内存分配

new: similar to malloc

delete: similar to free

`new`和`delete`可以是对象的构造函数和析构函数得到正确的调用

对于类对象，`new`会调用构造函数，`delete`会调用析构函数，这两者必须成对的使用

```cpp
int* p = new int;
*p = 5;
delete p;
```


## const

```cpp
int a[] = {53,54,55};

int * const p = a;
*p = 20;
p = a+1; // error
```

```cpp
const int* p = a;
*p = 20; // error 
p = a+1;
```