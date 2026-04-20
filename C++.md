### C++

#### 1. 初识

##### 1.1 基本结构

```c++
// 头
include<iostream>
// namespace
using namespace std;
// 输出
cout << "" << endl;
// endl 是换行符
cout << "" << 999 << endl;
```

- 常量：

  - 字面常量：用一次就废

    ```c++
    18;
    189.5;
    'a';
    "stronger";
    
    ```

  - 符号常量

- 标识符和关键字

- 全局定义：

  ```c++
  #define NUM 18
  ```

##### 1.2 指针

- 野指针：

  - 为避免野指针，需要及时初始化指针，或者把指针置空

- 空指针：

  ```c++
  int * p = NULL;
  int * p = nullptr;
  ```

  - NULL是C++内置的宏，表示空，本质为0
  - nullptr是C++11引入的关键字，表示指针为空
  - **空指针也不是什么光荣的事情，只在需要指针，但需要延迟赋值的场景下作为过渡使用**

- 指针运算：

  ```c++
  int main() {
      // 测试一下指针的运算
      int arr[] = {1, 2, 3, 4, 5};
      int * p = arr;
      cout << arr[0] << endl;
      cout << *(p+1) << endl;
      // 两个输出的数字是相同的，说明指针的运算是正确的
      return 0;
  }
  ```

- 指针循环遍历一下数组：

  ```c++
  int main() {
      // 两个数组
      int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9};
      int *p = arr;
  
      for (int i = 0; i < 9; i++) {
          cout << *(p + i) << " ";
      }
      return 0;
  }
  ```

- 动态内存分配：手动的在合适的实际时机对内存进行回收

  - new运算符申请的空间，使用delete删除

  ```c++
  int main() {
      int *p = new int[2];
      p[0] = 1;
      *(p + 1) = 2;
      cout << p[0] << " " << p[1] << endl;
      // 这是删除数组的格式
      delete []p;
  
      int *a = new int;
      *a = 999;
      cout << *a << endl;
      // 这是删除单个变量的格式
      delete a;
      return 0;
  }
  ```

- 指针悬挂：指针指向的区域已经被回收，称之为**指针悬挂**

  - 避免：不要轻易在指针之间相互赋值
  - delete回收之前，确保这个空间不再使用

- 常量指针：使用const修饰的指针

  - 第一种：指向区域的数据不变，但是指针还可以指向其他地方

    ```c++
    const 数据类型 * 指针
    数据类型 const * 指针
    ```

  - 第二种：指针本身不可以更改，但是指向的数据可以更改

    ```c++
    // 必须初始化地址，因为指针不可修改了
    数据类型 * const 指针 = 地址；
    ```

  - 第三种：指针的指向不可变，值也不可变

    ```c++
    const 数据类型 * const 指针 = 地址；
    ```

##### 1.3 结构体

```c++
struct Student {
    string name;
    int age;
    string gender;
};
```

##### 1.4 函数

- 懒得写了。。。没营养

##### 1.5 函数的值传递和地址传递

- 值传递是局部参数，不会影响域外的值
- 地址传递是直接修改了原本的值，看情况使用

##### 1.6 static关键字

- 可修饰变量和函数
- 比如static修饰函数中的静态变量，即使这个函数运行结束了，这个变量还在，一直持续到程序结束

#### 2. 面向对象

#### 2.1 类

```c++
class Person {
// 一片成员共用一个标签
public:
    // 构造函数
    Person() {
        cout << "对象被创建啦" << endl;
    }
};
```

- C++的权限访问规则：默认的是private
  - public：谁都可以访问
  - private：只有类自己能访问
  - protected：子类能访问

- 继承、多态、虚函数

  - 一个函数，如果子类想重新实现，就得用虚函数标明

    ```c++
    class Father {
    public:
        // 这个就是虚函数，加了virtual才会有多态
        virtual void say() {
            cout << "我是爸爸" << endl;
        }
    };
    ```

- 运算符重载：如下是代码示例

```c++
#include <iostream>
using namespace std;

class Point {
public:
    int x, y;

    Point(int x_, int y_) {
        x = x_;
        y = y_;
    }

    // 重载 + 号
    Point operator+(Point &other) {
        // 新点的x = 自己x + 对方x
        // 新点的y = 自己y + 对方y
        return Point(x + other.x, y + other.y);
    }
};

int main() {
    Point p1(1, 2);
    Point p2(3, 4);

    Point p3 = p1 + p2;  // 等价于 p1.operator+(p2)

    cout << p3.x << " " << p3.y << endl;  // 4 6
}
```

- 运算符重载，有 + - * /    还有 ==，这样的话，就类似Java里面的equal比较了，可以自定义比较的内容，回忆起来了吗！

#### 2.2 容器

- vector：就是个ArrayList

  ```c++
  	vector<int> v;
  
      v.push_back(10);    // 尾部添加
      v.push_back(20);
      v.push_back(30);
  ```

  

- string

- map：类似Java的TreeMap，key自动排序

  ```c++
  #include <map>
  map<int, string> m;
  m[1] = "张三";
  m[2] = "李四";
  cout << m[1] << endl;   // 张三
  // 遍历
  for (auto& pair : m) {
      cout << pair.first << ": " << pair.second << endl;
  }
  ```

- set：Java的TreeSet，可以自动去重 + 排序

  ```c++
  #include <set>
  
  set<int> st;
  st.insert(3);
  st.insert(1);
  st.insert(2);
  st.insert(3);   // 重复，自动忽略
  ```

- list：类似LinkedList，双向链表

  ```c++
  	list<int> lst;
      // 增
      lst.push_back(10);    // 尾插
      lst.push_front(20);   // 头插
      lst.push_back(30);
      // 遍历（不能用 []！）
      cout << "遍历：";
      for (int x : lst) {
          cout << x << " ";   // 20 10 30
      }
      cout << endl;
      // 删
      lst.pop_front();   // 删头
      lst.pop_back();    // 删尾
  ```

#### 2.3 常用的几个算法

- sort：

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> v = {3,1,4,1,5};
    
    sort(v.begin(), v.end());  // 排序
    
    for(int x : v) cout << x << " "; 
    // 输出：1 1 3 4 5
    
    // 从大到小排序
    sort(v.begin(), v.end(), greater<int>());
}
```

- find：查找元素

  - 在容器中查找某个值，找不到返回end()

  ```c++
  vector<int> v = {3,1,4};
  auto it = find(v.begin(), v.end(), 4);
  if(it != v.end()){
      cout << "找到了：" << *it;
  } else {
      cout << "没找到";
  }
  ```

  - reverse：反转（倒序）

    ```c++
    vector<int> v = {1,2,3};
    reverse(v.begin(), v.end());
    // 变成：3 2 1
    
    string s = "hello";
    reverse(s.begin(), s.end());
    // s 变成 "olleh"
    ```

#### 2.4 头文件相关

- 尖括号还是双引号：
  - 尖括号是系统自带的库
  - 双引号是找自己写的文件
- 头文件格式：

```c++
// Student.h
#ifndef STUDENT_H    // 固定开头，防止重复包含--AI解释的很好，忘记了直接问AI
#define STUDENT_H

#include <iostream>
#include <string>
using namespace std;
// 你的类写在这里
class Student {
public:
    string name;
};
#endif // STUDENT_H   // 固定结尾
```

















