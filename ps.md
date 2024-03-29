对文件描述符的理解

4G虚拟内存，在3G~4G内核地址空间中，存在PCB，也就是进程控制块

在PCB中，有一个文件描述符表，**文件描述符**就是这个表的下标，0代表STDIN，1代表STDOUT，2代表STDERR

然后每一个表项指针指向一个file结构体

这个file结构体中包含这个文件打开的次数，指向file_operations结构体的指针，指向dentry（目录项）结构体的指针，f_countno表示引用计数等等

file_openations结构体封装了许多和文件有关的库函数（read，write等等）

dentry结构体还有一个指针指向inode结构体，这个结构体存储了文件的所有者，文件权限，文件大小，类型，块的位置等等

为什么要有inode机制？

	1. 如果文件名包含特殊字符，无法正常删除。直接删除inode节点，也可以起到删除文件的作用
 	2. 移动文件或者重命名，不影响inode
 	3. 更新的时候，生成一个新的inode节点。等到下一次运行这个软件的时候，文件名就自动指向新的inode节点，旧的inode节点就会被回收



  mmap可以实现父子进程通信，是因为他们可以对同一块内存进行操作。mmap的原理是通过虚拟地址空间和文件磁盘位置的一个对应关系，通过对虚拟地址空间的操作，可以实现间接的对文件的操作



#### 函数指针，指针函数，指针数组，数组指针

基本解释：

> 函数指针

存放函数地址的一个指针

> 指针函数

首先这是一个函数，但是他的返回类型是一个地址，你需要用指针来进行保存

> 指针数组

一个数组，存放的元素是指针

> 数组指针

指向数组的一个指针

ps：

运算优先级

1.  ()[]  （这两个运算符优先级相等，如果都存在的话，按照从左到右的形式进行运算）
2. *
3. &

​	数组名和指针的概念：

数组名是数组的第一个元素的地址，对数组名取地址是代表一个数组的地址

```c
int a[3] = {1,2,3};
// a 代表这个数组的第一个元素的地址
// &a 代表这个数组的地址
// 这两个值是相同的，但是指向的类型是不同的，a指向的是int类型，&a指向的是int[3]*类型
```

数组指针

```c
int (*p)[4];
int a[3][4];
p = a;
p++;
// ()和[]运算符优先级相等，按照从左往右的方式进行运算，就是先定义一个指针，然后这个指针指向一个数组
// p++之后，指向的是 a[1][4]，还可以当做二维指针进行使用
```

指针数组

```c
int *p[4];
// *的优先级不如[],所以先进行[]，然后进行*，具体理解过程就是先进行数组的定义，然后再去定义这个数组的内部成员为指针
```

指针函数

```c
int t = 5;
int *cal(){
    cout << t << endl;
   // t++;
   // 注意这里不要返回局部变量的地址，当函数执行完毕的时候，
   // 在函数栈中申请的局部变量会收回那这个指针就变成了一个野指针
    return &t;
}
int main(){

    int* p = cal();
    cout << *p << endl;
    return 0;
}
//cal函数的返回一个是一个地址，我们用一个 int* 类型的指针接受这个函数的返回地址
```

函数指针

```c
int t = 5;
int cal(int x){
    cout << t << endl;
    return 1;
}
int main(){

    int (*p)(int x);
    // 首先定义函数指针，第一个是指针变量名，第二个是形参
    p = cal;
    // 将cal函数的首地址赋给指针
    int ret = p(5);
    cout << ret << endl;
    return 0;
}

```

