@[toc]


<br>

## 简述 const 关键字？
>const的作用是告诉编译器某个值是不变的，可以理解成只读，对变量起到保护作用。

**const可以用于以下方面：**
1. **修饰普通变量**
需要在一开始就进行初始化；

2. **修饰指针**
根据在 * 前后可以分为常量指针和指针常量，当然也可以前后都修饰，如`const int* a` ， `int* const  a`，`const int * const a`。

3. **修饰函数中的参数与返回值**
* 修饰函数中的参数：const在函数的参数中可以保护指针不被修改，如`strcpy(char* des, const char* src)`；
* 修饰函数的返回值：也可以保证返回值指针的内容不被修改，如 `const char* getStr()` 中，接收类型就必须是 `const char *`。

4. **修饰类中的成员变量与成员函数**
* 修饰成员函数时，不能对其成员变量进行修改（本质是修饰this指针）；且const修饰的成员函数可以被const，非const对象调用，但是普通成员函数只能被普通对象调用。
* 修饰成员变量时，必须在构造函数列表里进行初始化。 


 **延伸用法：const + &**

如`const string& s`，在满足的`引用传递的优点`下，既可以保护`别名不被修改`，也可以
`接收右值`（接收右值的原因在于右值都是const属性且不可见，只有const传递能捕捉到）。

<br>


------------------------------------------

### 简述 static 关键字？
>**static 修饰的数据存放在全局数据区，限定了作用域，并且生命周期是直到程序结束。**

**C中的用法：**
1. **静态局部变量**
一般用在函数中，当函数第一次被调用时会在`全局数据区初始化`，其作用域只存在于该`函数`中。
2. **静态全局变量**
静态全局变量不能为其他文件所用，`作用域只在该文件中`。
3. **静态函数**
与静态全局变量的作用类似，静态函数不能为其他文件所用，`作用域只在该文件中`。

**C++中的用法（包含C中的用法）：**
1. **static修饰类成员变量**
静态成员变量`属于整个类`，在`类实例之间共享`，也就是说无论创建多少个类的对象，该成员变量都只有一个副本。
同时由于静态成员变量属于整个类，所以`只能在类内申明，在类外初始化`。如果在类内就初始化，那么会导致每一个实例化的对象都拥有一个该成员变量，这是矛盾的。

2. **static修饰类成员函数**
静态成员函数属于整个类，`由于没有this指针，所以只能调用静态成员变量`；

<br>

----------------
#### new/delete 和 malloc/free 的区别？
1. **最重要的一点**
`new/delete`可以调用类的构造函数，析构函数等；而`malloc/free`只是简单的申请和释放内存。
2. **本质类型**
new属于运算符，可重载；而malloc属于库函数，不可重载。
3. **参数**
new不需要参数就能够自动分配空间大小，malloc则需要传入申请内存块的大小。
4. **返回值**
new的返回值是相应类型的指针，malloc返回值是`void*`类型。
5. **申请内存失败**
new申请失败会抛出`bad_alloc` 异常，而malloc申请失败则会返回`NULL`。
6. **申请区域**
new操作从 自由存储区 申请内存， malloc 从堆区申请内存；自由存储区可以是堆区，也可以是全局静态数据区，这由`operator new(size_t)` 决定。
7. **处理数组**
new[]和delete[]必须配套使用来处理数组。

**延伸1: 既然new/delete的功能已经完全覆盖了malloc/free，为什么还要保留malloc/free呢？**
>**因为C++程序中经常会使用C，而C只能使用malloc/free进行动态内存申请和释放。**

<br>

**延伸2：写出重载new delete 的程序**
* 使用new运算符时，先调用 `operator new(size_t)`申请空间，再调用构造函数，然后返回指针；
* 使用delete运算符时，先调用析构函数，再调用 `operator delete(void*)`释放空间。

```cpp
class A
{
public:
    A() { cout << "A()" << endl; }
    ~A() { cout << "~A()" << endl; }

    void*  operator new(size_t n)
    {
        cout << "operator new(size_t)" << endl;
        void *ret = malloc(n);
        return ret;
    }

    void* operator new[](size_t n)
    {
        cout << "operator new[](size_t)" << endl;
        void *ret = malloc(n);
        return ret;    
    }

    void operator delete(void* p)
    {
        if (p)
        {
            cout << "operator delete(void*)" << endl;
            free(p);
            p = nullptr;
        }
    }

    void operator delete[](void* p)
    {
        if (p)
        {
            cout << "operator delete[](void*)" << endl;
            free(p);
            p = nullptr;
        }
    }
};
```
<br>

------------
#### 简述extern关键字？
**extern关键字有两个作用：**
1. **跟”C”连用时**
`告诉编译器用C的规则进行编译`。因为如果使用C++的规则，由于C++支持函数的重载，所以会将函数名进行修改，导致程序报错。
2. **不与”C”进行连用时**
对于变量或函数只进行申明而不是定义，`提示编译器该变量或函数的定义需要在另一个文件中寻找`。在编译阶段，目标模块A虽然找不到extern 修饰的变量或者函数，但不会报错，而是在链接时从模块B中找到该函数或变量。

<br>

-------------------

