# Smart Pointers

智能指针的行为类似于常规指针，重要的区别是它负责自动释放所指向的对象

新标准库提供的两种智能指针的区别在于管理底层指针的方式：
- `std::shared_ptr`：允许多个指针指向同一个对象
- `std::unique_ptr`：独占所指向的对象
- `std::weak_ptr`：伴随类，弱引用，指向`std::shared_ptr`所指向的对象，但不拥有它


## 1. `std::shared_ptr`

类似`vector`，`std::shared_ptr`是一个模板类
```cpp
shared_ptr<string> p1;
shared_ptr<list<int>> p2;
```

默认初始化的智能指针中保存着一个空指针

智能指针的使用方法与普通指针类似，解引用一个智能指针返回它所指向的对象。如果在一个条件判断中使用智能指针，效果就是检测它是否为空

```cpp
if(p1 && p1->empty()){
    *p1 = "hi";
    }

```

`shared_ptr`和`unique_ptr`都支持的操作

- `shared_ptr<T> p``unique_ptr<T> p`：空智能指针，可以指向类型为T的对象
- `p`: 将`p`作为一个条件判断，若`p`指向一个对象，则为真
- `*p`: 解引用，返回`p`所指向的对象
- `p->mem`: 等价于`(*p).mem`
- `p.get()`: 返回`p`中保存的指针，要小心使用，若智能指针释放了其对象，返回的指针所指的对象也消失了
- `swap(p,q)`: 交换`p`和`q`中的指针 `p.swap(q)`也可以

`shared_ptr`独有的操作：
- `make_shared<T>(args...)`: 返回一个`shard_ptr`,指向一个动态分配的类型为T的对象，args是传递给T的构造函数的参数
- `shared_ptr<T>p(q)`: `p`是`shared_ptr q`的拷贝，此操作会递增`q`中的计数器，`q`中的指针必须能转化为`T*`
- `p=q`: `p`和`q`都是`shared_ptr`,所保存的指针必须能够相互转换，此操作会递减`p`的引用次数，递增`q`的引用次数，若`p`的引用次数变为0，`p`所指向的对象会被释放
- `p.unique()`: 返回`p`的引用计数器是否为1
- `p.use_count()`: 返回与`p`共享对象的智能指针数量
