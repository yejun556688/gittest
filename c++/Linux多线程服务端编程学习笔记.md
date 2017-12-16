#### __thread关键字

`__thread`是GCC内置的线程局部存储设施（thread local storage)。它的实现非常高效，比`pthread_key_t`快很多。`__thread`变量的存取效率可与全局变量相比，每个线程有一份独立实体，各线程的变量值互不干扰。[p97]

```c++
int g_var;   // 全局变量
__thread int t_var;  // __thread变量
```

