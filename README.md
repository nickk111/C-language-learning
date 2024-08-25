# Chapter 5: Pointers and Dynamic Memory Management

A pointer in C/C++ can be used to access a specific memory location and has very good efficiency. It is one of the unique advantages of C/C++, and also is a challenging knowledge point. You must be very careful to use it. Otherwise, some bugs will tend to be introduced.

译：C/C++ 中的指针可用于访问特定的内存位置，具有非常高的效率。这是 C/C++ 的独特优势之一，也是一个具有挑战性的知识点。你必须非常小心地使用它。否则，往往会引入一些错误。

## Pointers

Firstly, we should understand that a pointer is a variable for an address. Like other kinds of variables, a pointer has its value, and the value is an address in the memory.

译：首先，我们应该明白，指针是一个地址变量。和其他类型的变量一样，指针也有自己的值，而这个值就是内存中的地址。

There is an operator `&` which can return the address of a variable or an object. If we have a variable `int num = 10;`, we can get the address of `num` by `&num`. So we can declare a pointer and assign an address to it.

译：运算符 `&` 可以返回变量或对象的地址。如果我们有一个变量 `int num = 10;`，我们可以通过 `&num` 获得 `num` 的地址。因此，我们可以声明一个指针并为其赋值。

```cpp
int num = 10;
int * p1 = NULL, * p2 = NULL; // declaration two pointers, initialized to 0
p1 = &num; // take the address of num, assign to p1
p2 = &num; // take the address of num, assign to p2
*p1 = 20; // assign 20 to num
*p2 = 30; // assign 30 to num
```

![Two pointers point to the same memory whose address is 0xABCDEF00.](images/pointer.png)

In the previous source code, two pointers, `p1` and `p2`, are declared. Both of them are assigned the address of `num`. It means the two pointers point to the same memory, and the two pointers regard there is an `int` at that position of the memory.

译：在前面的源代码中，声明了两个指针 `p1` 和 `p2`。这两个指针都分配了 `num` 的地址。这意味着这两个指针指向同一个内存，并且这两个指针认为内存的该位置有一个 `int`。

Another operator here is `*` which is for pointer dereference. It can dereference the pointer to access the object. The following two lines are equivalent since `p1` is a pointer to `num`.

译：这里的另一个运算符是指针反引用运算符 `*`。它可以反引用指针来访问对象。下面两行是等价的，因为 `p1` 是指向 `num` 的指针。

```cpp
*p1 = 20;
num = 20;
```

Pointers can not only point to some fundamental types such as `int`, `float`, but also point to objects of structures or objects and even functions. In the following code, a structure `stu` is declared and initialized. Then a pointer `pStu` is initialized to the address of `stu`. The structure and the pointer are illustrated in the following figure.

译：指针不仅可以指向一些基本类型，如 `int` 和 `float`，还可以指向结构体对象或类对象，甚至函数。在下面的代码中，我们声明并初始化了一个结构体 `stu`。然后将指针 `pStu` 初始化为 `stu` 的地址。结构和指针如下图所示。

Since `stu` is a structure, we can use the dot operator `.` like `stu.name` to access its members. We can also use the pointer of `stu` to do that, but the operator will be `->`, not be `.`. `stu.name` is equivalent to `pStu->name`.

译：由于 `stu` 是一个结构体，我们可以像 `stu.name` 一样使用点操作符 `.` 来访问其成员。我们也可以使用 `stu` 的指针来这样做，但操作符将是 `->`，而不是 `.`。`stu.name` 等同于 `pStu->name`。

```cpp
struct Student
{
    char name[4];
    int born;
    bool male; 
};
//declare and initialize a structure
Student stu = {"Yu", 2000, true};
//assign the address of stu to pStu
Student * pStu = &stu;
//change members of the structure through pointer pStu
strncpy(pStu->name, "Li", 4);
pStu->born = 2001;
(*pStu).born = 2002;
pStu->male = false;
```

![Pointer `pStu` points to a structure in memory that contains three members, name, born and male.](images/pointer-struct.png)

The address values contained in pointers can be printed out as shown in the following code.

译：如下代码所示，可以打印出指针中包含的地址值。

```cpp
printf("Address of stu: %p\n", pStu); //C style
cout << "Address of stu: " << pStu << endl; //C++ style
cout << "Address of stu: " << &stu << endl;
cout << "Address of member name: " << &(pStu->name) << endl;
cout << "Address of member born: " << &(pStu->born) << endl;
cout << "Address of member male: " << &(pStu->male) << endl;
```

The address is an unsigned integer. It is a 32-bit unsigned integer on most 32-bit OS, and a 64-bit unsigned integer for most current 64-bit systems. You can run the following code to check how many bits your system uses for addresses.

译：地址是一个无符号整数。在大多数 32 位操作系统中，它是一个 32 位无符号整数，而在目前大多数 64 位系统中，它是一个 64 位无符号整数。你可以运行下面的代码来检查你的系统使用了多少位地址。

```cpp
cout << "sizeof(int *) = " << sizeof(int *) << endl; // 4 or 8
cout << "sizeof(Student *) = " << sizeof(Student *) << endl; // 4 or 8
cout << "sizeof(pStu) = " << sizeof(pStu) << endl; // 4 or 8
```

### Pointers of pointers

Since a pointer is a variable, the variable will also be stored in memory and has its address. Then another pointer can point to this pointer. The figure shows the variable `num` in the example source code. The pointer `p` points to `num`, and the pointer `pp` points to `p`. `*(*pp) = 20` will change the value of `num` to `20`, and it is equivalent to `*p = 20` and `num = 20`.

译：由于指针是一个变量，因此该变量也将存储在内存中，并拥有其地址。然后，另一个指针可以指向这个指针。图中显示了示例源代码中的变量 `num`。指针 `p` 指向 `num`，指针 `pp` 指向 `p`。`*(*pp) = 20` 将把 `num` 的值改为 `20`，相当于 `*p = 20` 和 `num = 20`。

![Pointer `pp` points to `p`, and pointer `p` points to `num`.](images/pointer-pointer.png)

```cpp
//pointer-pointer.cpp
#include <iostream>
using namespace std;

int main()
{
    int num = 10;
    int * p = &num;
    int ** pp = &p;
    *(*pp) = 20;

    cout << "num = " << num << endl;

    return 0;
}
```

### Constant pointers

If the `const` type qualifier is put before a fundamental type like `const int num = 1;`, the value of `num` cannot be changed after its initialization.

译：如果在基本类型（如 `const int num = 1;`）前加上 `const` 类型限定符，则 `num` 的值在初始化后不能更改。

If `const` is put before the type name of a pointer as in the following example, you cannot change the value that the pointer points to.

译：如果在指针的类型名之前加上 `const` 就不能更改指针指向的值，如下例所示。

```cpp
int num = 1;
//You cannot change the value that p1 points to through p1
const int * p1 = &num;
*p1 = 3; //error
num = 3; //okay
```

But you can change the pointer itself.译：但你可以改变指针本身。

```cpp
p1 = &another; //okay
```

`const` can also be put between `*` and the name. If so, the pointer will point to that memory permanently, and cannot point to other places. But the value in that memory can be changed.

译：也可以将 `const` 放在 `*` 和名称之间。如果是这样，指针将永久指向该内存，而不能指向其他地方。但该内存中的值可以更改。

```cpp
//You cannot change value of p2 (address)
int * const p2 = &num;
*p2 = 3; //okay
p2 = &another; //error
```

If two `const` are used as follows, then neither the address nor the value can be changed.译：如果如下使用两个 `const`，则地址和值都不能更改。
```cpp
//You can change neither
const int* const p3 = &num;
*p3 = 3; //error
p3 = &another; // error
```

### Pointers and arrays

The elements in an array are also stored in memory and have their addresses. The following code shows how to get the addresses of the first 4 elements in an array and assign them to 4 pointer variables. After printing out the addresses, you can find those addresses have an interval of `sizeof(Student)`. Member `born` of the second element can be accessed by `students[1].born` or `p1->born`.

译：数组中的元素也存储在内存中，并有自己的地址。下面的代码展示了如何获取数组中前 4 个元素的地址并将其赋值给 4 个指针变量。打印出地址后，可以发现这些地址的间隔为 `sizeof(Student)`。第二个元素的成员 `born` 可以通过 `students[1].born` 或 `p1->born` 访问。

```cpp
//pointer-array.cpp
Student students[128];
Student * p0 = &students[0];
Student * p1 = &students[1];
Student * p2 = &students[2];
Student * p3 = &students[3];

printf("p0 = %p\n", p0);
printf("p1 = %p\n", p1);
printf("p2 = %p\n", p2);
printf("p3 = %p\n", p3);

//the same behavior
students[1].born = 2000;
p1->born = 2000;
```

The output of the previous source code on my computer is译：之前的源代码在我电脑上的输出结果是
```bash
p0 = 0x16f3d6e58
p1 = 0x16f3d6e64
p2 = 0x16f3d6e70
p3 = 0x16f3d6e7c
```

You can consider an array name as a pointer. The difference between an array name and a pointer is that an array name can only point to the same memory. An array name can be regarded as a constant pointer to a memory location permanently.

译：你可以将数组名视为指针。数组名和指针的区别在于，数组名只能指向同一个内存。数组名可视为永久指向内存位置的常量指针。

We print the value of `&students`, `students` and `&students[0]`, and it can be found that the three addresses are the same and are all the address of the first element in array `students`.

译：我们打印`&students`、`students`和`&students[0]`的值，可以发现这三个地址相同，都是数组`students`中第一个元素的地址。

```cpp
//pointer-array.cpp
printf("&students = %p\n", &students);
printf("students = %p\n", students);
printf("&students[0] = %p\n", &students[0]);
```

The output of the previous source code on my computer is译：之前的源代码在我电脑上的输出结果是

```bash
&students = 0x16f3d6e58
students = 0x16f3d6e58
&students[0] = 0x16f3d6e58
```
If we assign an array (the address) to a pointer `p`, then `p` can be used in an array style (`p[0]`) to access the elements. `p` is a pointer to a position of the memory and regards that position as the starting address of an object. But `p` does not know how many elements are there. We must be very careful with the out-of-bound error.

译：如果我们将一个数组（地址）赋值给一个指针 `p`，那么 `p` 就可以用数组方式（`p[0]`）来访问元素。`p` 是指向内存某个位置的指针，并将该位置视为对象的起始地址。但 `p` 并不知道有多少个元素。我们必须非常小心地处理越界错误。

```cpp
//pointer-array.cpp
Student * p = students;
p[0].born = 2000;
p[1].born = 2001;
p[2].born = 2002;
```

Since the value of a pointer is an address, an integer number, you can perform arithmetic operations on a pointer. But be careful that the address will not increase 1 after you perform `p + 1`. `p + num` or `num + p` points to the **num-th** element of the array `p`. `p - num` points to the **(-num)-th** element. The address value changes according to the data type. If the data type is `float *`, then the address will increase 4 bytes after `p + 1` or `p++`.

译：由于指针的值是一个地址，一个整数，因此可以对指针进行算术运算。但要注意，在执行 `p + 1` 之后，地址不会增加 1。`p + num` 或 `num + p` 指向数组 `p` 的第**个**元素。p - num指向第**（-num）-**个元素。地址值根据数据类型而改变。如果数据类型为float *，则地址将在p + 1或p++` 之后增加 4 个字节。

```cpp
//arithmetic.cpp
int numbers[4] = {0, 1, 2, 3};
int * p = numbers + 1; // point to the element with value 1
p++; // point to the element with value 2

*p = 20; //change number[2] from 2 to 20
*(p-1) = 10; //change number[1] from 1 to 10
p[1] = 30; //change number[3] from 3 to 30
```

![After the operation and assignments, the pointer and the array should be as shown in the figure.](images/pointer-arithmetic.png)

We do not know how many elements followed the address that a pointer points to, and we also do not know if a pointer is from an integer or an integer array. The following code can be compiled successfully, but the memory accessing will be out-of-bound.

译：我们不知道指针指向的地址后面有多少个元素，也不知道指针是来自整数还是整数数组。以下代码可以成功编译，但内存访问将超出边界。

```cpp
//bound.cpp
int num = 0;
int * p = &num;
p[-1] = 2; //out of bound
p[0] = 3; //okay
*(p+1) = 4; //out of bound
````


## Allocate memory: C style

When a program is executed by the operating system (OS), the OS will allocate memory for the program. The memory for a program can be divided into 5 segments as shown in the following figure. 

译：操作系统（OS）执行程序时，会为程序分配内存。程序的内存可分为 5 段，如下图所示。

![Different data segments of a program.](images/data-segments.png)

* Code: It contains executable code. It is read-only and fixed size.
* Data: It contains initialized static variables including global static and local static ones.
* BSS: BSS section contains uninitialized static data.
* Heap: It contains dynamically allocated memory. The memory allocated by `malloc()` or `new` is in this segment.
* Stack: Local variables and call stack are stored in it.
* 代码： 它包含可执行代码。它是只读的，大小固定。
* 数据： 它包含初始化的静态变量，包括全局静态变量和局部静态变量。
* BSS：BSS 部分包含未初始化的静态数据。
* 堆：堆包含动态分配的内存。通过 `malloc()` 或 `new` 分配的内存位于该段中。
* 栈： 本地变量和调用堆栈存储在其中。

## Allocate memory: C++ style



## Lab

* allocate memory for an array string, modify elements by integer values one by one, then print out the result as a string. Please try to modify the element out of range and see what will happen.
* 译：为数组字符串分配内存，逐个修改元素的整数值，然后将结果打印为字符串。请尝试修改超出范围的元素，看看会发生什么。
