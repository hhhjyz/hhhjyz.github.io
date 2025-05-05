# Operator Overloading

How do operators work with classes?
- 我们可以像声明函数一样声明operator
- When we use that operator with our new object, it performs a custom function or operation
- Just like in function overloading, if we give it the same name, it will override the operator’s behavior!

不能被重载的运算符：
- Scope Resolution`::`
- Ternary`?`
- Member Access`.`
- Pointer-to-member access`.*`
- Object size, type and casting: `sizeof()` `typeid()` `case()`

overload的两种方式：
- Member overloading
    - 在自己的类中进行重载的声明
- Non-Member overloading
    - 在类定义之外进行声明
    - Define both the left and right hand objects as parameters

**Non-Member overloading** 

this is actually prederred by the STL , and is more idiomatic C++

1. 允许左手边（操作数）为非类类型。
2. 允许我们对那些我们并不拥有其定义的类重载操作符。  
    a. 我们可以定义一个操作符来将一个斯坦福大学学号（StanfordID）与你所定义的其他自定义类进行比较。
 
```tabs

tab: Non-member Operator Overloading
`
bool operator< (const StanfordID& lhs, const StanfordID& rhs);
`
tab: Member Operator Overloading

`bool StanfordID::operator< (const StanfordID& rhs) const{}`


```


The keyword allows non-member functions or classes to access private information in another class!
```cpp
class StanfordID { 
private: 
std::string name; 
std::string sunet; 
int idNumber; 
public: 
// constructor for our StudentID 
StanfordID(std::string name, std::string sunet, int idNumber); 
//.... 
friend bool operator < (const StanfordID& lhs, const StanfordID& rhs); }
```

