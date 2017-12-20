#### 4.5 善用__thread关键字

​	`__thread`是GCC内置的线程局部存储设施（thread local storage)。它的实现非常高效，比`pthread_key_t`快很多。`__thread`变量的存取效率可与全局变量相比，每个线程有一份独立实体，各线程的变量值互不干扰。[p97]

```c++
int g_var;   // 全局变量
__thread int t_var;  // __thread变量
```

​	`__thread`使用规则：只能用于修饰POD类型，不能修饰class类型，因为无法自动调用构造函数和析够函数。`__thread`可修饰**全局变量**、函数内的**静态变量**，但不能修饰函数的局部变量或class的普通成员变量。另外，`__thread`变量初始化只能用编译器期常量。

#### 4.6 多线程与IO