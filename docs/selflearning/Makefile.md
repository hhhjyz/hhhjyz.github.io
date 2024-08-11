# Makefile 的撰写

- 多文件编程中，分别编译并链接，生成可执行文件

## Makefile：进行编译的脚本文件

### version1

```makefile

## VERSION1
hello: main.cpp printhello.cpp factorial.cpp
	g++ -o hell main.cpp printhello.cpp factorial
```

```make```命令会自动在当前目录下寻找makefile文件

- 会自动检查源文件和可执行文件之间的先后顺序
- 通过执行下面的这行命令来生成hello可执行文件，依赖于hello后面的这三个文件

生成hello可执行文件

### version2

```makefile
CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.o
$(TARGET): $OBJ #hello依赖于后面的这些文件，如果OBJ文件更新，执行下面的文件
	$(CXX) -o $(TARGET) $(OBJ)
# OBJ文件怎么来的
main.o: main.cpp
	$(CXX) -c main.cpp
printhello.o: printhello.cpp
	$(CXX) -c printhello.cpp
factorial.o: factorial.cpp
	$(CXX) -c factorial.cpp

```

先生成三个```.o```文件

### VERSION3

```makefile
CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.o

CXXFLAGS = -c -Wall#(编译选项)

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^# $@代表冒号前面的,$^代表冒号后面的，箭头往上指指的是所有的
	
%.o: %.cpp#所有的cpp文件生成.o文件
	$(CXX) $(CXXFLAGS) $< -o $@#箭头往左指知的是冒号后的第一个
.PHONY: clean#伪目标，永远不会生成
clean:
	rm -f *.o $(TARGET)
#清除所有.o文件

```

```-Wall```指报所有的warning

### VERSION4

``````makefile
CXX = g++
TARGET = hello
SRC = $(wildCARD *.cpp)#所有当前目录下的.cpp文件都放到这个变量里
OBJ = $(patsubst %.cpp, %.O, $(SRC))#patsubst：路径替换

CXXFLAGS = -c  -Wall

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^# $@代表冒号前面的,$^代表冒号后面的，箭头往上指指的是所有的
	
%.o: %.cpp#所有的cpp文件生成.o文件
	$(CXX) $(CXXFLAGS) $< -o $@#箭头往左指知的是冒号后的第一个
.PHONY: clean#伪目标，永远不会生成
clean:
	rm -f *.o $(TARGET)
``````



