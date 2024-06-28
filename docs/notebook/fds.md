# The Fundamental of Data Structure

## Chapter 5 Hashing

### Hashing

- Hash Table的实现叫做Hashing

- Hashing 以常数平均时间执行插入、删除和查找，但是不支持FindMin,FindMax,已经线性时间将排过序的整个表进行打印的操作

  ### General Idea

- Hash Table是一个包含key的具有固定大小的数组

- key是一个带有相关value的string

- collision： 两个key hashing 到同一个值
- load factor($\lambda$): Hash Table中元素个数与tablesize的比值

### Hash Function

- Hash Table 的TableSize最好是Prime

- $f(x)$必须容易计算，且能最小化冲突的可能
- uniform hash function: $f(x)$将所有key平均分配到hash table 中

**example**:
$$
f(x)=(\sum x[N-i-1]*32^i)
$$
将27改成32，用左移5位的运算代替乘以26的运算，效率更高

### Open Hashing ( Separate Chaining)

将hashing 到同一个值的所有元素保留到一个表中，每一个表都有表头

![image-20240625161055135](C:\Users\17439\AppData\Roaming\Typora\typora-user-images\image-20240625161055135.png)

- 初始化

```c
struct ListNode;
typedef struct ListNode * Position;
struct HashTbl;
typedef struct HashTbl * HashTable;

struct ListNode
{
    ElementType Element;
    Position Next;
};
typedef Position List;

/* List *TheList will be an array of lists, allocated later */ 
/* The lists use headers (for simplicity), */ 
/* though this wastes space */ 
struct HashTbl
{
    int TableSize;
    List * TheLists;
};
```

- 创建空表

  ```c
  HashTable InitializeTable(int TableSize)
  {
      HashTable H;
      int i;
      if (TableSize < MinTableSize)
      {
          Error("Table size too small");
          return NULL;
      }
      H = (HashTable)malloc(sizeof(struct HashTbl)); // Allocate table
      if (H == NULL) 
          FatalError("Out of Space!!!");
      H->TableSize = NextPrime(TableSize);  // Better be prime
      H->TheLists = malloc(sizeof(List) * H->TableSize);  // Array of lists
      if (H->TheLists == NULL)   
          FatalError("Out of space!!!"); 
      for(i = 0; i < H->TableSize; i++) 
      {   // Allocate list headers
          H->TheLists[i] = malloc(sizeof(struct ListNode)); // Slow! 
          if ( H->TheLists[i] == NULL )  
              FatalError("Out of space!!!"); 
          else    
              H->TheLists[i]->Next = NULL;
      } 
      return H; 
  }
  ```

  

- 在Hash Table中寻找Key

  ```c
  Position Find(ElementType Key, HashTable H)
  {
      Position P;
      List L;
  
      L = H->TheLists[Hash(Key, H->TableSize)];
  
      P = L->Next;
      // Identical to the code to perfome a Find for List ADT
      while (P != NULL && P->Element != Key) // Probably need strcmp
          P = P->Next;
      return P;
  }
  
  ```

  

- 插入

  ```c
  void Insert(ElementType Key, HashTable H)
  {
      Position Pos, NewWell;
      List L;
      Pos = Find(Key, H);
      if (Pos == NULL) // Key is not found, then insert
      {
          NewCell = (Position)malloc(sizeof(struct ListNode));
          if (NewCell == NULL)
              FatalError("Out of space!!!");
          else
          {
              L = H->TheLists[Hash(Key, H->TableSize)];
              NewCell->Next = L->Next;
              NewCell->Element = Key; // Probably need strcpy
              L->Next = NewCell;
          }
      }
  }
  ```

  ### Open Addressing

- 通过寻找下一个空的单元来解决collision

  ```c
  Algorithm: insert key into an array of hash table
  {
      index = hash(key);
      initialize i = 0  // the counter of probing
      while (collision at index)
      {
          index = (hash(key) + f(i)) % TableSize;
          if (table is full) break;
          else i++;
      }
      if (table is full)
          ERROR("No space left");
      else
          Insert key at index;
  }
  
  ```

  

- $f(i)$为冲突解决函数，初始状态为$f(0)=0$
- 通常用于$\lambda<0.5$的情况

#### Linear Probing

​	$f$是i的线性函数，典型情况是$f(i)=i$

### 