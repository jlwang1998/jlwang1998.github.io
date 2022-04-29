---
title: Essential C++
date: 2022-01-08 20:00:00 +0800
categories: [C++]
tags: [侯捷]
pin: true
author: 王家乐

toc: true
comments: true
typora-root-url: ../../jlwang1998.github.io
math: false
mermaid: true
---

# 第1章 C++编程基础

## 命名空间namespace

## 初始化语法：构造函数语法

```c++
int num_trier(0)
```

## 模板类(class template)：必须在类名之后的尖括号内指定其元素类型

## *pi：指针的值；pi：指针的地址；&ival：数值ival的地址

## 读写文件：

```c++
#include<fstream>

//不希望丢弃原文件内容，用追加模式打开
ofsream outfile("seq_data.txt",ios__base::app);

//读取文件
ifstream infile("seq_data.txt");

//同时读写文件
fstream iofile("seq_data.txt",ios__base::in||ios__base::app);
if(!iofile)
    //由于某种原因，文件无法打开
else
{
    //开始读取之前，将文件重新定位至起始处
    iofile.seekg(0);
}
```

## #include<iomanip>：

io代表输入输出，manip是manipulator（操纵器）的缩写。

主要是对cin,cout之类的一些操纵运算子，比如setfill，setw，setbase，setprecision等等。它是I/O流控制头文件,就像C里面的格式化输出一样.以下是一些常见的控制函数的：

```c++
cout << setw( 3 ) << 1 << setw( 3 ) << 10 << setw( 3 ) << 100 << endl; 
```

```c++
#输出结果为
_ _1_10100 //（默认是右对齐）当输出长度大于3时(<<1000)，setw(3)不起作用。
```

▲setw(n)用法： 通俗地讲就是预设宽度

# 第2章 面向对象的编程风格

## 传值和传址

局部函数调用时，传值的话不会修改原参数的值，只会重新复制一个对象，称为”程序堆栈“。要想修改原参数，就需要用&取址符号，船址。

## 将参数声明为reference的理由&

希望得以直接对所传入的对象进行修改；降低复制大型对象的额外负担。

## reference&、指针*

pointer参数和reference参数的差异：pointer可能(也可能不)指向某个实际对象。当我们提领pointer时，一定要先确定其值为非0。而reference必定代表某个对象，所以不需要作此检查。

```c++
int ival = 1024;
int *pi = &ival; //表示pi为ival的地址，*pi为ival的值1024
int &rval = ival; //表示rval和ival为一个对象，两者地址相同
```

```C++
int *pi;
pi = new int(1024); //pi = 1024的地址，*pi为1024
```



```C++
//将vector的参数声明为reference

#include<iostream>
#include<vector>
#include<fstream>
using namespace std;

void display(vector<int>&vec)
{
	for(int i=0;i<vec.size();i++)
	{
		cout<<vec[i]<<' ';
	}
	cout<<endl;
}
int main()
{
	int arr[8] = {8,34,3,13,1,21,5,2};
	vector<int>vec(arr,arr+8);
	cout<<"vector before sort: ";
	display(vec);

	return 0;
}
```

```c++
//将vector以指针pointer传递

#include<iostream>
#include<vector>
#include<fstream>
using namespace std;

void display(vector<int>*vec)
{
    //在提领pointer时，要先确定其值非0
	if(!vec)
	{
		cout<<"display():the vector pointer is 0\n";
		return;
	}
	for(int i=0;i<vec->size();i++)
	{
		cout<<(*vec)[i]<<' ';
	}
	cout<<endl;
}

int main()
{
	int arr[8] = {8,34,3,13,1,21,5,2};
	vector<int>vec(arr,arr+8);
	cout<<"vector before sort: ";
	display(vec);

	return 0;
}
```

##  作用域及范围

对象如果在函数外部声明，则该对象的内存在main()开始执行之前就已经分配好，可以一直存在至程序结束。

内置类型的对象如果定义在file scope内，将被初始化为0；如果被定义于local scope之内，除非指定其初值，否则不会被初始化。

## 动态内存管理

```c++
int *pi;
pi = new int(1024);

int *pia = new int[24]//从heap中分配数组

delete pi; //释放pi所指的对象
delete []pia;
```

## 提供默认参数值 

默认参数值规则：

(1) 解析操作由最右边开始进行，如果为某个参数提供了默认值，那么这一参数右侧的所有参数都必须也具有默认参数值。

```c++
//错误：没有为vec提供默认值
void display(ostream &os=cout,const vector<int>&vec);
```

(2) 默认值只能指定一次

## 声明inline函数

将经常被调用、体积小的函数声明为inline，改善性能。

```c++
bool is_size_ok(int size)
{
	const int max_size = 1024;
	if(size<=0||size>max_size)
	{
		cerr<<"invalid size\n";
		return false;
	}
	return true;
}


const vector<int>*
fibon_seq(int size)
{
	const int max_size = 1024;
	static vector<int>elems;
	
	if(!is_size_ok(size))
		return 0;
	
	for(int i=elems.size();i<size;i++)
	{
		if(i==0||i==1)
		{
			elems.push_back(1);
		}
		else
		{
			elems.push_back(elems[i-1]+elems[i-2]);
		}
	}
	return &elems;
}
//返回Fibonacci数列中位置为pos的元素
inline bool fibon_elem(int pos,int &elem)
{
	const vector<int>*pseq = fibon_seq(pos);
	if(!pseq)
	{
		elem=0;
		return false;
	}
	elem = (*pseq)[pos-1];
	return true;
}
```

## 函数重载

函数自动根据参数类型来调用函数。

```c++
#include<iostream>
using namespace std;

void add(int a,int b)
{
        cout<<"print a integer :"<<a+b<<endl;
}

void add(float a,float b)
{
        cout<<"print a float :"<<a+b<<endl;
}

int main()
{
        add(1,2);
        add(1.1,2.1);
        return 0;
}
```

## 定义并使用模板函数

在函数重载时，定义函数时，每个函数非常相像，只有变量类型不同，使用模板函数解决。只需要定义一份函数内容。

```
#include<iostream>
using namespace std;

template<typename elemType>
void add(elemType a,elemType b)
{
        cout<<a+b<<endl;
}

int main()
{
		int a=1,b=2;
		float c=1.1,d=2.1;
        add(a,b);
        add(c,d);
        return 0;
}
```

## 函数指针

```c++
const vector<int> *fibon_seq(int size);
const vector<int> *lucas_seq(int size);
const vector<int> *pell_seq(int size);
const vector<int> *triang_seq(int size);
const vector<int> *square_seq(int size);
const vector<int> *pent_seq(int size);
```

为了获得该数组的第pos个数，我们采用函数指针，这样不需要提供6个不同的函数，只需定义一个。

```c++
//返回Fibonacci数列中位置为pos的元素
inline bool fibon_elem(int pos,int &elem)
{
	const vector<int>*pseq = fibon_seq(pos);  //(A)
	if(!pseq)
	{
		elem=0;
		return false;
	}
	elem = (*pseq)[pos-1];
	return true;
}
```

只有A处不同，用函数指针代替。

**函数指针：**必须指明其所指函数的返回类型及参数列表。本例中函数的返回类型是`const vector<int> *`，参数列表是`int`，函数指针的定义必须将*放在某个位置，表示这份定义所表现的是一个指针。

```c++
const vector<int>* (*seq_ptr)(int)
```

```c++
// 函数指针
inline bool fibon_elem(int pos,int &elem,const vector<int>* (*seq_ptr)(int))
{
    //检验指针是否指向某个函数
    if(!seq_ptr)
    {
        cout<<"Internal Error:seq_ptr is set to null!";
    }
	const vector<int>*pseq = seq_ptr(pos);
	if(!pseq)
	{
		elem=0;
		return false;
	}
	elem = (*pseq)[pos-1];
	return true;
}
```

```c++
// 函数指针数组
const vector<int>* (*seq_array[])(int) = {
    fibon_seq,ns_lucas,ns_pent,
    ns_triang,ns_square,ns_pent
};

//循环调用
int seq_index = 0;
while(next_seq==true)
{
    seq_ptr = seq_array[++seq_index];
}
```

## 设定头文件

函数定义只能有一份，inline函数例外，在每个调用点上，编译器都得取得其定义。因此，必须将inline函数定义在头文件中。

```c++
//将seq_array定义于file scope，需要在头文件声明
const int seq_cnt = 6; //const object喝inline一样，一出文件之外便不可见，因此不需要加extern
const vector<int>* (*seq_array[seq_cnt])(int);//错误写法，会被解读为定义
extern const vector<int>* (*seq_array[seq_cnt])(int); //正确，声明
```

包含头文件时，如果头文件喝包含此文件的程序代码文件位于同一个磁盘目录下，便使用双引号，否则使用尖括号。

```c++
#include"NumSeq.h"
#include<NumSeq.h>
```

# 第3章 泛型编程风格

STL由两种组件组成：

（1）容器：vector、list、set、map；

（2）泛型算法：find()、sort()、replace()、merge()

vector和list是顺序性容器，顺序性容器会依次维护第一个、第二个……直到最后一个元素。

map和set是关联性容器，关联性容器让我们快速查找容器中的元素值。map是key/value组合，key用于查找，value用来表示我们要储存或取出的数据。set仅含有key，可对其进行查询操作，来判断某值是否存在于其中。

## 3.1 指针的算术运算

### array[2]和*(array+2)

array以第一个元素的指针传入函数，可以通过下标访问array中的每一个元素。如同此array是个对象(而非指针形式)一般。下标操作就是将array的起始地址加上索引值来产生某个元素的地址。然后对该地址进行提领（dereference）操作。

```c++
array[2];
*(array+2);//结果一样
```

```c++
//无论数组元素的类型是什么，都可以访问数组中的每一个元素

template<typename elemType>
elemType* find(const elemType *first,const elemType *last,const elemType &value)
{
    if(!first||!last)
        return 0;
    //当first!=last，就把value拿来和first所指元素比较
    for(;first!=last;++first)
    {
        if(*first==value)
            return frist;
    }
    return 0;
}
```

## 3.2 了解Iterator(泛型指针)

```c++
// iter被定义为一个iterator，指向一个vector，后者的元素类型为string。其初值指向svec的第一个元素。
// 双冒号::表示此iterator是位于string vector定义内的嵌套(nested)类型
vector<string>::iterator iter = svec.begin()
```

```c++
//用iterator取代下标运算符
template<typename elemType>
void display(const vector<elemType>&vec,ostream &os)
{
    vector<elemType>::const_iterator iter = vec.begin();
    vector<elemType>::const_iterator end_iter = vec.end();
    for(;iter!=end_iter;++iter)
    {
        os<<*iter<<' ';
    }
    os<<endl;
}
```

```c++
// 重新实现find()，使得其支持一对指针或者一对指向某种容器的iterator
template<typename IteratorType,typename elemType>
IteratorType find(IteratorType first,IteratorType last,const elemType &value)
{
    for(;first!last;++first)
    {
        if(value==*first)
            return first;
    }
    return last;
}

//应用
const int asize = 8;
int ia[asize] = {1,1,2,3,5,8,13,21};
vector<int>ivec(ia,ia+asize);
list<int>ilist(ia,ia+asize);
int *pia = find(ia,ia+asize,1024);
if(pia!=ia+asize)
    //找到了......

vector<int>::iterator it;
it = find(ivec.begin(),ivec.end(),1024);
if(it!=ivec.end())
    //找到了......
    
list<int>::iterator iter;
iter = find(ilist.begin(),ilist.end(),1024);
if(iter!=ilist.end())
    //找到了......
```

## 3.3 使用顺序性容器

vector、list、deque

deque：对于最前端元素的插入和删除，效率较高。

### 容器的插入函数insert()

```c++
iterator insert(iterator position,elemType value);//在position之前插入value
void insert(iterator position,int count,elemType value)；//在position之前插入count个value
void insert(iterator1 position,iterator2 first,iterator2 last);//在position之前插入[firsy,last)所标识的各个元素
```

### 容器的删除操作(erase)

```c++
iterator erase(iterator position);//删除position指定的元素
iterator erase(iterator first,iterator last);//删除[first,last)范围内的元素
```

## 3.5 使用泛型算法

```c++
#include<algorithm>
```

四种泛型搜索算法：

（1）find()：用于搜索无序集合中是否存在某值；

（2）binary_search()：用于有序集合的搜素。如果搜索到目标，就返回true。binary_search()比find()更有效率；

（3）count()：返回数值相符的元素数目；

（4）search()：比对某个容器内是否存在某个子序列，如果存在，就返回一个iterator指向子序列的起始处，否则指向容器末尾。

## 3.6 如何设计一个泛型算法

### Function Object

```c++
sort(vec.begin(),vec.end(),greater<int>()); //按降序排序
```

### Function Object Adapter

binder adapter(绑定适配器)会将function object的参数绑定至某个特定值。bind1st将指定值绑定至第一操作数，bind2nd将指定值绑定至第二操作数。

```c++
vector<int>filter_ver1(const vector<int>&vec,int filter_val,less<int>&lt)
{
	vector<int>nvec;
    vector<int>::const_iterator iter = vec.begin();
    
    while((iter=find_if(iter,vec.end(),bind2nd(lt,val)))!=vec.end())
    {
        nvec.push_back(*iter);
        iter++;
    }
	return nvec;
}
```

```c++
// 消除filter()与vector元素类型，以及filter()与vector容器类型的依赖关系，使filter()更加泛型化

#include<iostream>
#include<vector>
#include<string>
#include<algorithm>
using namespace std;


template<typename InputIterator,typename OutputIterator,typename ElemType,typename Comp>
OutputIterator filter(InputIterator first,InputIterator last,OutputIterator at,const ElemType &val,Comp pred)
{
	while((first=find_if(first,last,bind2nd(pred,val)))!=last)
	{
		cout<<"found value: "<<*first<<endl;
		*at++ = *first++;
	}
	return at;	
}


int main()
{
	const int elem_size = 8;
	int ia[elem_size] = {12,8,43,0,6,21,3,7};
	vector<int>ivec(ia,ia+elem_size);
	
	//储存过滤结果
	int ia2[elem_size];
	vector<int>ivec2(elem_size);
	
	cout<<"filtering integer array for values less than 8\n";
	filter(ia,ia+elem_size,ia2,elem_size,less<int>());
	
	cout<<"filtering integer vector for values greater than 8\n";
	filter(ivec.begin(),ivec.end(),ivec2.begin(),elem_size,greater<int>());
	
	return 0;
}
```

`not1`可对`unary function object`的真伪值取反，not2可对`binary function object`的真伪值取反。

```c++
while((iter=find_if(iter,vec.end(),not1(bind2nd(lt,val)))!=vec.end())
```

## 3.7 使用map

```c++
#include<map>
#include<string>
map<string,int>words;
//循环打印所有单词出现的次数
map<string,int>::iterator it = words.begin();
for(;it!=words.end();++it)
{
    cout<<"keys: "<<it->first
        <<"value: "<<it->second<<endl;
}

//查询map内是否有某个key
//利用find()函数,如果存在key，find()函数返回key/value形成的pair；不存在返回end()
int count = 0;
map<string,int>::iterator it;
it = words.find("vermeer");
if(it!=words.end())
    count = it->seond;

//利用count()函数，返回某特定项在map内的个数
int count = 0;
string search_word("vermeer");

if(words.count(search_word))
    count = word[search_word];
```

## 3.8 使用Set

Set由一群key组合而成，对于任何key值只能存储一份。

```c++
#include<set>
#include<string>
//定义一个用来记录“排除字眼”的set
set<string>word_exclusion;
while(cin>>tword)
{
    if(word_exclusion.count(tward))
        continue;//不执行后续语句，进入下一轮while循环    
    words[tward]++;
}

//为set插入单一元素
iset.insert(ival);
//为set插入某个范围的元素
iset.insert(vec.begin(),vec.end());
```

## 3.9 如何使用Iterator Inserter

将源端容器中每一个符合条件的元素一一幅值至目的端容器时，目的端容器必须足够大。以前的方法是将目的端容器大小设置为与源端容器大小相同，这样子会占用较大内存。

C++标准库提供3个insertion adapter：

（1）`back_inserter()`：以容器的`push_back()`取代赋值运算符。传入的参数就是容器本身。

```c++
vector<int>result_vec;
unique_copy(ivec.begin(),ivec.end(),back_inserter(result_vec));
```

（2）`inserter()`：以容器的`insert()`函数取代`assignment`运算符。接收参数为`容器和iterator`，iterator指向插入操作的起点。

```c++
vector<string>svec_res;
unique_copy(svec.begin(),svec.end(),inserter(svec_res,svec_res.end()));
```

（3）`front_inserter()`：以容器的`push_front()`函数取代`assignment`运算符。只适用于`list`和`deque`

```c++
list<int>ilist_clone;
copy(ilist.begin(),ilist.end(),front_inserter(ilist_clone));
```

需要包含头文件：

```c++
#include<iterator>
```

将3.6节的main函数改写为：

```c++
int main()
{
	const int elem_size = 8;
	int ia[elem_size] = {12,8,43,0,6,21,3,7};
	vector<int>ivec(ia,ia+elem_size);
	
	//储存过滤结果
	int ia2[elem_size];
	vector<int>ivec2(elem_size);
	
	cout<<"filtering integer array for values less than 8\n";
	filter(ia,ia+elem_size,ia2,elem_size,less<int>());//以上inserter不能用于array
	
	cout<<"filtering integer vector for values greater than 8\n";
	filter(ivec.begin(),ivec.end(),back_inserter(ivec2),elem_size,greater<int>());
	
	return 0;
}
```

## 3.10 使用iostream Iterator

```c++
#include<iostream>
#include<string>
#include<vector>
#include<algorithm>
#include<iterator>
#include<fstream>
using namespace std;

int main()
{
	/*
	//从文件读取
	ifstream in_file("input_file.txt");
	ofstream out_file("out_file.txt");
	
	if(!in_file||!out_file)
	{
		cerr<<"!!unable to open the necessary files.\n";
		return -1;
	}
	istream_iterator<string>is(infile);//first iterator
	istream_iterator<string>eof; //在定义istream_iterator时，不指定对象，就代表为last iterator
	
	vector<string>text;
	copy(is,eof,back_inserter(text));//泛型算法copy()
	
	ostream_iterator<string>os(out_file," "); //第一个参数为字符串输出位置，第二个参数为各个元素被输出时的分隔符
	copy(text.begin(),text.end(),os); //copy()函数将text每一个元素一一写到os所表示的ostream上
	*/
	//从标准输入输出设备读取
	istream_iterator<string>is(cin);//first iterator
	istream_iterator<string>eof; //在定义istream_iterator时，不指定对象，就代表为last iterator
	
	vector<string>text;
	copy(is,eof,back_inserter(text));//泛型算法copy()
	
	sort(text.begin(),text.end());
	
	ostream_iterator<string>os(cout," "); //第一个参数为字符串输出位置，第二个参数为各个元素被输出时的分隔符
	copy(text.begin(),text.end(),os); //copy()函数将text每一个元素一一写到os所表示的ostream上
	
	
	return 0;
}
```

### 泛型算法partition()

std::partition会将区间[first,last)中的元素重新排列，满足判断条件pred的元素会被放在区间的前段，不满足pred的元素会被放在区间的后段。可以用来区分奇偶。该算法不能保证元素的初始相对位置，如果需要保证初始相对位置，应该使用stable_partition.

```c++
#include<bits/stdc++.h>
using namespace std;
bool cmp(string s){
    return s.size()<=5;
}
int main(){
    vector<string> v1 = {"fjskf", "fjslfjksl", "fjsklfk", "f", "fdds", "fjs", "fjslf"};
    vector<string> v2 = {"fjskf", "fjslfjksl", "fjsklfk", "f", "fdds", "fjs", "fjslf"};
    auto x1=partition(v1.begin(),v1.end(),cmp());//将满足条件的放在前面，不满足条件的放在后面，不保证稳定。
    auto x2=stable_partition(v2.begin(),v2.end(),cmp());//将满足条件的放在前面，不满足条件的放在后面，保证稳定。
}
```

# 第4章 基于对象的编程风格

class由两部分组成：公开的(public)操作函数、私有的(private)实现细节。

## 4.1 如何实现一个class

Class声明：`class Stack;`

```c++
//class 定义
class Stack{
public:
    bool push(const string&);
    bool pop(string &elem);
    bool peek(string &elem);
    bool find(const string&)const;
    bool empty();
    bool full();
    
    //size()定义与class本身中
    int size(){return _stack.size();}
private:
    vector<string>_stack;
};

//在class主体之外定义成员函数
//::类作用域解析运算符
inline bool Stack::empty()
{
    return _stack.empty();
}

bool Stack::pop(string &elem)
{
    if(empty())
    {
        return false;
    }
    elem = _stack.back();
    _stack.pop_back();
    return true;
}
bool Stack::find(const string &elem)const
{
	return ::find(_stack.begin(),_stack.end(),elem)!=_stack.end();//:;全局作用域，如果不加，会递归到自己的find()
}
```

## 4.2 什么是构造函数和析构函数

### 构造函数(Constructor)

用来初始化data member，函数名称必须和类名相同，可以被重载。

```c++
class Triangular{
public:
    //一组重载的constructor
    Triangular();//默认
    Triangular(int len);
    Triangular(int len,int beg_pos);
    
private:
    string name;
    int _length;
    int _beg_pos;
    int _next;
};

//构造函数初始化,法一
Triangular::Triangular(int len,int bp)
{
    _length = len>0?len:1;
    _beg_pos = bp>0?bp:1;
    _next = _bet_pos-1;
}

//法二：成员初始化列表,主要用于将参数传给member class object
Triangular::Triangular(int len,int bp)
    :_name("Triangular")   //将Triangular传给_name
{
    _length = len>0?len:1;
    _beg_pos = bp>0?bp:1;
    _next = _bet_pos-1;
}
```

### 析构函数(destructor)

用于释放在constructor中或对象生命周期中分配的资源。名称为：类名加上'~'的前缀，不能被重载。

```c++
class Matrix{
    public:
    Matrix(int row,int col)
        :_row(row),_col(col)
    {
		_pmat = new double(row*col);
    }
    ~Matrix()
    {
        delete []_pmat; //释放内存
    }
private:
    int _row,_col;
    double* _pmat;
};
```

### 成员逐一初始化

```c++
Triangular tri1(8);
Triangular tri2 = tri1; //_length、_beg_pos、_next会依次从tri1复制到tri2.
```

成员逐一初始化可能导致已经释放内存的数组重新进行释放内存操作（对指针操作时），会报错。为防止这个问题，通过提供一个`copy constructor`。

```c++
Matrix:Matrix(const Matrix &rhs)
    :_row(rhs._row),_col(rhs._col)
{
    //对rhs._pmat所指的数组产生一份完全副本
    int elem_cnt = _row*_col;
    _pmat = new double[elem_cnt];
    for(int ix=0;ix<elem_cnt;++ix)
    {
        _pmat[ix] = rhs._pmat[ix];
    }
}
```

## 4.3 何谓mutable（可变）和const（不变）

```c++
int sum(const Triangular &train)
{
    int beg_pos = train.beg_pos();
    int length = train.length();
    int sum = 0;
    for(int ix=0;ix<length;++ix)
        sum+=train.elem(beg_pos+ix);
    return sum;
}
```

train为`const reference`参数，train不能被修改。需要在调用train的相关函数时，加上const。

```c++
int length() const{return _length;}
```

当想要修改与类状态无关的数据成员，加上关键字`mutable`。

```c++
class Triangular{
public:
    bool next(int &val)const;
    void next_reset()const {_next = _beg_pos-1;}
    
private:
    mutable int _next;  //修改_next不会被视为改变了class object的状态
    int _beg_pos;
    int _length;
}
```

## 4.4 什么是this指针

this指针：在成员函数内指向其调用者(一个对象)。

```c++
Triangular& Triangular::copy(const Triangular &rhs)
{
    if(this!=rhs)
    {
        _length = rhs._length;
        _beg_pos = rhs._beg_pos;
        _next = rhs._beg_pos-1;
    }
    return *this;
}
```

## 4.5 静态类成员

### static data member

表示唯一的、可共享的member，只有唯一的一份实体，类似全局对象。

```c++
class Triangular{
public:
    //...
private:
    static vector<int>_elems;
};

//在程序代码文件中进行定义
vector<int>Triangular::_elems;
```

### static member function

一般情况下，成员函数必须通过某个类的对象进行调用（实例化对象，通过对象来调用成员函数）。

```c++
bool Triangular::is_elem(int value)
{
    if(!_elems.size()||_elems[_elems.size()-1]<value)
        gen_elems_to_value(value);
    
    vector<int>::iterator found_it;
    vector<int>::iterator end_it = _elems.end();
    found_it = find(_elems.begin(),end_it,value);
    return found_it!=end_it;
}
```

`is_elem()`并未访问任何`non_static data member`，就无需通过类的对象进行调用。

```c++
if(Triangular::is_elem(8)) //这样调用即可
```

## 4.6 打造一个Iterator Class

为Iterator Class定义!-、*、++等运算符

```c++
class Triangular_iterator
{
public:
    Triangular_iterator(int index):_index(index-1){};
    bool operator==(const Triangular_iterator&)const;
    bool operator!=(const Triangular_iterator&)const;
    int operator*()const;
    Triangular_iterator& operator++(); //前置++
    Triangular_iterator& operator++(int); //后置++
private:
    void check_integrity()const;
    int _index;
}

inline bool Triangular_iterator::operator==(const Triangular_iterator &ths)const
{
    return _index==rhs._index;  //判断两个Triangular_iterator的_index是否相等
}

inline int Triangular_iterator::operator*()const
{
    check_integrity();
    return Triangular::_elems[_index];
}

inline void Triangular_iterator check_integrity()const
{
    if(_index>=Triangular::_max_elems)
        throw iterator_overflow();
    
    if(_index>=Triangular::_elems.size())
        Triangular::gen_elements(_index+1);
}

//前置++
inline Triangular_iterator& Triangular_iterator::operator++()
{
    ++_index;
    check_integrity();
    return *this;
}

//后置++
 inline Triangular_iterator& Triangular_iterator::operator++(int)
{
    Triangular_iterator tmp = *this; 
    ++_index;
    check_integrity();
    return tmp;;
}
```

## 4.7 友元friend

为了访问private member，可以在某个函数原型前加上关键字friend。

```c++
//将重载函数声明为某个class的friend
class Triangular{
    friend int Triangular_iterator::operator*();
    friend void Triangular_iterator::check_integrity();
}
```

## 4.8 实现一个function object

function object是一种“提供有function call运算符”的class。

```c++
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

class LessThan{
public:
		LessThan(int val):_val(val){}
		int comp_val()const {return _val;} //基值的读取
		void comp_val(int nval){_val=nval;} //基值的写入
		bool operator()(int _value)const;
private:
			int _val;
};

inline bool LessThan::operator()(int value)const {return value<_val;}

int count_less_than(const vector<int>&vec,int comp)
{
	LessThan It(comp);
	
	int count = 0;
	for(int ix=0;ix<vec.size();++ix)
	{
		if(It(vec[ix]))
		++count;
	}
	return count;
}


void print_less_than(const vector<int>&vec,int comp,ostream &os=cout)
{
	LessThan It(comp);
	vector<int>::const_iterator iter = vec.begin();
	vector<int>::const_iterator it_end = vec.end();
	
	os<<"elements less than "<<It.comp_val()<<endl;
	while((iter=find_if(iter,it_end,It))!=it_end)
	{
		os<<*iter<<' ';
		++iter;
	}
}

int main()
{
	int ia[16] = {17,12,44,9,18,45,6,14,
	23,67,9,0,27,55,8,16};
	vector<int>vec(ia,ia+16);
	
	int comp_val=20;
	cout<<"Number of elements less than "<<comp_val<<" are "<<count_less_than(vec,comp_val)<<endl;
	print_less_than(vec,comp_val);
		
	return 0;
}
```

## 4.10 重载iostream运算符

```c++
//重载output运算符
ostream& operator<<(ostream &os,const Triangular &ths)
{
    os<<"("<<rhs.beg_pos()<<","<<rhs.length()<<")";
    rhs.display(rhs.length(),rhs.beg_pos(),os);
    return os;
}

//重载input运算符
istream& operator>>(istream &is,Triangular &rhs)
{
    char ch1,ch2;
    int bp,len;
    //假设输入为(3,6) 6 10 15 21 28 36
    //那么ch1=='('，bp==3，ch2==','，len==6
    is>>ch1>>bp>>ch2>>len;
    
    rhs.beg_pos(bp);
    rhs.length(len);
    rhs.next_reset();
    
    return is;
}
```

```c++
//调用output运算符
Triangular tri(6,3);
cout<<tri<<"\n";

//输出结果
(3,6) 6 10 15 21 28 36
```

## 4.11 指针，指向Class Member Function

将PtrType声明为一个指针，指向num_sequence的member function

```c++
typedef void(num_sequence::*PtrType)(int);
PtrType pm=0; 
```

```c++
class num_sequence{
public:
	typedef void(num_sequence::*PtrType)(int);
    
    //_pmf可指向下列任何一个函数
    void fibonacci(int);
    void pell(int);
    void lucas(int);
    void triangular(int);
    void sequare(int);
    void pentagonal(int);
private:
    vector<int>* _elem; //指向目前所用的vector
    PtrType _pmf;
    static const int num_seq=7;
    static PtrType func_tbl[num_seq];
    static vector<vector<int> >seq;
};
```

定义一个指针，并指向成员函数`fibonacci()`

```c++
PtrType pm = &num_sequence::fibonacci;
```

对`static data member`定义

```
const int num_sequence::num_seq;
vector<vector<int> >num_sequence::seq(num_seq);

num_sequence::PtrType num_sequence::func_tbl[num_seq] = 
{
	0,
	&num_sequence::fibonacci,
	&num_sequence::pell,
	&num_sequence::lucas,
	&num_sequence::triangular,
	&num_sequence::sequare,
	&num_sequence::pentagonal
}
```

## 习题

### enum枚举

```c++
enum Roster {Tom, Sharon, Bill, Teresa, John};
```

该语句将创建一个名为 Roster 的数据类型。因为单词 enum 是 C++ 关键字，所以它必须小写，值得注意的是，数据类型本身的名字是以大写字母开头的。虽然这并非必须，但是绝大多数程序员都会釆用首字母大写的形式。

和 Roster 数据类型关联的命名整数常量被称为枚举量，Roster 数据类型的变量可能只是关联到这些枚举量的值之一，但它们的值是什么呢？

默认情况下，编译器设置第一个枚举量为 0，下一个为 1，以此类推。在上述示例中，Tom 的值将是 0，Sharon 的值为 1，等等。最后一个枚举量 John 的值为 4。

### itoa()

```c++
itoa():char *itoa( int value, char *string,int radix);
```

value：欲转换的数据。
string：目标字符串的地址。
radix：转换后的进制数，可以是10进制、16进制等，范围必须在 2-36。

功能：将整数value 转换成字符串存入string 指向的内存空间 ,radix 为转换时所用基数(保存到字符串中的数据的进制基数)。
返回值：函数返回一个指向 str，无错误返回。

# 第5章 面向对象编程风格

## 5.1 面向对象编程概念

**特征：**继承、多态、动态绑定。

**多态：**让基类的pointer或reference得以十分透明地指向任何一个派生类地对象。

**动态绑定：**每次loan_check_in()执行时，仅能在执行过程中依据mat所指地实际对象来决定调用哪一个check_in()。找出实际被调用地究竟是哪一个派生类地check_in()函数这一解析操作会延时到运行时进行。

## 5.2 漫游：面向对象编程地思维

默认情况下，成员函数的解析在编译时静态地进行，若要令其在运行时动态进行，需要在它的声明前加上关键字`virtual`。静态成员函数无法被声明为虚函数。

被声明为`protected`的所有成员都可以被派生类直接访问，除了派生类之外，都不能直接访问protected成员。

```c++
#include<iostream>
#include<string>
using namespace std;

class LibMat{
public:
	LibMat()
	{
		cout<<"LibMat::LibMat() default constructor!\n";
	}
	virtual ~LibMat()
	{
		cout<<"LibMat::~LibMat() destructor!\n";
	}
	virtual void print()const
	{
		cout<<"LibMat::print()--I am a LibMat object!\n";
	}
};

class Book:public LibMat{
public:
	Book(const string &title,const string &author):_title(title),_author(author)
	{
		cout<<"Book::Book("<<_title<<","<<_author<<") constructor\n";
	}
	virtual ~Book()
	{
		cout<<"Book::~Book()destructor!\n";
	}
	virtual void print()const
	{
		cout<<"Book::print()--I am a Book object!\n"
			<<"My title is: "<<_title<<'\n'
			<<"My author is: "<<_author<<'\n';
	}
	const string& title()const
	{
		return _title;
	}
	const string& author()const
	{
		return _author;
	}
protected:
	string _title;
	string _author;
};

class AudioBook:public Book{
public:
	AudioBook(const string &title,const string &author,const string &narrator):Book(title,author),_narrator(narrator)
	{
		cout<<"AudioBook::AudioBook( "<<_title<<", "<<_author<<", "<<_narrator<<") constructor\n";
	}
	~AudioBook()
	{
		cout<<"AudioBook::~AudioBook()destructor\n";
	}
	
	virtual void print()const
	{
		cout<<"AudioBook::print()--I am a AudioBook object!\n"
			<<"My title is:" <<_title<<'\n'
			<<"My author is: "<<_author<<'\n'
			<<"My narrator is: "<<_narrator<<endl;
	}
	const string& narrator()
	{
		return _narrator;
	}
protected:
	string _narrator;
};

void print(const LibMat &mat)
{
	cout<<"in global print():about to print mat.print()\n";
	mat.print();
}

int main()
{
//	cout<<"\n"<<"Creating a LibMat object to print()\n";
//	LibMat libmat;
//	print(libmat);
	AudioBook ab("Mason and Dixon","Thomax Pynchon","Edwin Leonard");	
	print(ab);	
	return 0;
}
```

## 5.4 定义一个抽象的基类

**设计抽象类的步骤：**

（1）找到所有子类共通的操作行为；

（2）找到哪些操作行为与类型相关，也就是说，有哪些行为必须根据不同的派生类而有不同的实现方式。这些操作行为应该成为整个类继承体系中的虚函数。

（3）找到每个操作行为的访问层级：public、private、protected

**注意：**

（1）每个虚函数。要么得有其定义，要么可设为`纯虚函数`。如果对于该类而言，这个虚函数并无实际意义，那么令他为纯虚函数。

```c++
virtual int elem(int pos)const = 0;
```

（2）根据一般规则，凡基类定义有一个或多个虚函数，应将其析构函数声明为`virtual`。

```c++
class num_sequence(){
public:
    virtual ~num_sequence(){};
}；
```



```c++
class num_sequence{
public:
    virtual ~num_sequence(){};
    
    virtual int elem(int pos)const = 0;
    virtual const char* what_am_i()const = 0;
    static int max_elems() {return _max_elems;}
    virtual ostream& print(ostream &os=cout)const = 0;
protected:
    virtual void gen_elems(int pos)const = 0;
    bool check_integrity(int pos,int size)const;
    
    const static int _max_elems = 1024;
};

bool num_sequence::check_integrity(int pos,int size)const
{
    if(pos<=0||pos>_max_elems)
    {
        cerr<<"!!invalid position: "<<pos<<"Cannot honor request\n";
        return false;
    }
    if(pos>size)
    {
        gen_elems(pos);
    }
    return true;
}

ostream& operator<<(ostream &os,const num_sequence &ns)
{
    return ns.print(os);
}
```

## 5.5 定义一个派生类

**派生类由两部分组成：**

（1）基类构成的子对象，由基类的`non-static data member`组成；

（2）派生类的部分（由派生类的`non-static data member`组成）。

**注意：**

（1）基类的public member在派生类中同样是public，开放给派生类的用户使用；基类中的protected member在派生类中是protected，只能给后续派生类使用，无法给目前这个派生类使用；

（2）当派生类有某个member与其基类的member同名时，便会遮掩住基类的那份member。如果要在派生类内使用继承来的那份member，必须利用class scope运算符加以限定。

```c++
class Fibonacci:public num_sequence{
public:
    Fibonacci(int len=1,int beg_pos=1):_length(len),_beg_pos(beg_pos){}
    
    virtual int elem(int pos)const;
    virtual const char* what_am_i()const{return "Fibonacci";}
    virtual ostream& print(ostream &os=cout)const;
    int length()const {return _length;}
    int beg_pos()const {return _beg_pos;}
protected:
    virtual void gen_elems(int pos)const;

    int _length;
    int _beg_pos;
    static vector<int> _elems;
};

int Fibonacci::elem(int pos)const
{
    if(!check_integrity(pos,_elems.size())) //调用的是继承来的check_integrity()
    {
        return 0;
    }
    if(pos>_elems.size())
    {
        Fibonacci::gen_elems(pos);//通过class scope运算符，告诉编译器，调用的是Fibonacci派生类的gen_elems()
    }
    return _elems[pos-1];
}

void Fibonacci::gen_elems(int pos)const
{
    if(_elems.empty())
    {
        _elems.push_back(1);
        _elems.push_back(1);
    }
    if(_elems.size()<=pos)
    {
        int ix = _elems.size();
        int n_2 = _elems[ix-2];
        int n_1 = _elems[ix-1];
        
        for(;ix<=pos;++ix)
        {
            int elem = n_2+n_1;
            _elems.push_back(elem);
            n_2=n_1;n_1=elem;
        }
    }
}

ostream& Fibonacci::print(ostream &os)const
{
    int elem_pos = _beg_pos-1;
    int end_pos = elem_pos + _length;
    if(end_pos>_elems.size())
    {
        Fibonacci::gen_elems(end_pos);
    }
    while(elem_pos<end_pos)
    {
        os<<_elems[elem_pos++]<<' ';
    }
    return os;
}

int main()
{
    Fibonacci fib;
    cout<<"fib:beginning at element 1 for 1 element: "<<fib<<endl;
    
    Fibonacci fib2(16);
    cout<<"fib2:beginning at element 1 for 16 element: "<<fib2<<endl;
    
    Fibonacci fib3(8,12);
    cout<<"fib3:beginning at element 12 for 8 element: "<<fib3<<endl;
}
```

## 5.7 基类应该多么抽象

在当前的设计下，抽象基类只提供接口，并未提供任何实现。当需要加入新的数列类时，比较繁琐。提出新的设计方式：将所以派生类共有的实现内容剥离出来，移至基类内。

```c++
class num_sequence{
public:
    virtual ~num_sequence(){};
    
    virtual int elem(int pos)const = 0;
    virtual const char* what_am_i()const = 0;
    int elem(int pos)const;
    ostream& print(ostream &os=cout)const;
    
    int length()const {return _length;}
    int beg_pos()const {return _beg_pos;}
    static int max_elems() {return 64;}
protected:
    virtual void gen_elems(int pos)const = 0;
    bool check_integrity(int pos,int size)const;
    //num_sequence是抽象基类，无法为它定义任何对象，所以声明为protected
    num_sequence(int len,int bp,vector<int>&re):_length(len),_beg_pos(bp),_relems(re){}
    
    int _length;
    int _beg_pos;
    vector<int> &_relems;
};
```

```c++
class Fibonacci:public num_sequence{
public:
    Fibonacci(int len=1,int beg_pos=1);
    virtual const char* what_am_i()const
    {
        return "Fibonacci";
    }
    
protected:
    virtual void gen_elems(int pos)const;
    static vector<int>_elems;
};
```

## 5.9 在派生类中定义一个虚函数

当定义派生类时，必须决定，究竟要将基类中的虚函数覆盖掉，还是原封不动地加以继承。如果我们继承了纯虚函数，那么派生类也将被视为抽象类，无法为它定义任何对象。

如果要覆盖掉基类提供地虚函数，派生类定义地函数原型必须完全符合基类所声明的函数原型，包括：参数列表、返回类型、常量性(const-ness)。

### 虚函数的静态解析

派生类虚函数机制不会出现预期行为：

（1）基类的constructor和destructor内

当我们构造派生类对象时，基类的constructor会先被调用，如果在基类的constructor中调用某个虚函数，此时调用的就是基类中的虚函数。

（2）当我们使用的是基类的对象，而非基类对象的pointer或reference时

```c++
void print(LibMat object,const LibMat *pointer,const LibMat &reference)
{
    object.print();//此时调用的是LibMat::print()
    
    //会通过虚函数机制进行解析
    pointer->print();
    reference.print();
}
```

## 5.10 运行时的类型鉴定机制

每一个类中都有一份`what_am_i()`函数，为了简化，利用运行时的类型鉴定机制。

```c++
#include<typeinfo>

inline const char* num_sequence::what_am_i()const
{
    return typeid(*this).name();
}

if(typeid(*ps)==typeid(Fibonacci))
{
    Fibonacci *pf = static_cast<Fibonacci*>(ps);//经过static_cast运算符转换指针变为派生类指针
    pf->gen_elems(64);
}
//static_cast有个潜在危险，就是编译器无法确定转换类指针从基类到派生类是不是转换对了
//使用dynamic_cast运算符提供有条件的转换，可避免这个危险

if(Fibonacci *pf = dynamic_cast<Fibonacci*>(os))
    pf->gen_elems(64);
```

# 第6章 以template进行编程

**二叉树的遍历：**

（1）前序遍历：被遍历的节点本身先被打印，然后打印左子树内容，最后打印右子树内容；

（2）中序遍历：先打印左子树，然后是节点本身，最后是右子树；

（3）后续遍历：先打印左子树，再打印右子树，最后是节点本身。

## 6.2 Class Template的定义

```c++
template<typename elemType>
class BinaryTree{
public:
    BinaryTree();
    BinaryTree(const BinaryTree&);
    ~BinaryTree();
    BinaryTree& operator=(const BinaryTree&);
    
    bool empty(){return _root==0};
    void clear()
    {
        if(_root)
            clear(_root);
        _root = 0;
    }
private:
    void clear(BTnode<elemType>*)
    BTnode<elemType> *_root;
    //将src所指子树复制到tar所指子树
    void copy(BTnode<elemType>*tar,BTnode<elemType>*src);
};

template<typename elemType>
inline BinaryTree<elemType>::BinaryTree():_root(0){}

template<typename elemType>
inline BinaryTree<elemType>::BinaryTree(const BinaryTree &rhs)
{
    copy(_root,rhs._root);
}

template<typename elemType>
inline BinaryTree<elemType>::~BinaryTree()
{
    clear();
}

template<typename elemType>
inline BinaryTree<elemType>& BinaryTree<elemType>::operator=(const BinaryTree &rhs)
{
    if(this!=&rhs)
    {
        clear();
        copy(_root,rhs._root);
    }
    return *this;
}
```

## 6.3 Template类型参数的处理

将所有的template类型参数视为"class类型"，应使用下列的初始化方法。

```c++
template<typename valType>
inline BTnode<valType>::BTnode(const valType &val):_val(val) //将valType视为class类型
{
    _cnt=1;
    _lchild = _rchild=0;
}
```

## 6.4 实现一个Class Template

**插入某个新值：**

```c++
template<typename elemType>
inline void BinaryTree<elemType>::insert(const elemType &elem)
{
    if(!_root)
    {
        _root = new BTnode<elemType>(elem);//如果根节点设为设定，就分配一块新的BTnode需要的内存空间
    }
    else
    {
        _root->insert_value(elem);
    }
}

//小于根节点的值放在左子树，大于的放在右子树，每个数值在数中只出现一次，使用cnt记录该节点插入的次数
template<typename valType>
void BTnode<valType>::insert_value(const valType &val)
{
    if(val==_val)
    {
        _cnt++;
        return;
    }
    if(val<_val)//小于根节点，放在左子树
    {
        if(!_lchild)
        {
            _lchild = new BTnode(val);
        }
        else
        {
            _lchild->insert_value(val);
        }
    }
    else //大于根节点，放在右子树
    {
        if(!_rchild)
        {
            _rchild = new BTnode(val);
        }
        else
        {
            _rchild->insert_value(val);
        }
    }
}
```

**移除某值：**以节点的右子节点取代节点本身，然后搬移左子节点，使它成为右子节点的左子树的叶节点。如果此刻无右子节点，那么就以左子节点取代节点本身。将根节点的移除操作以特例处理。

```c++
template<typename elemType>
inline void BinaryTree<elemType>::remove(const elemType &elem)
{
    if(_root)
    {
        if(_root->val==elem)
            remove_root();
        else
            _root->remove_value(elem,_root);
    }
}

//搬移左子节点
template<typename elemType>
void BTnode<valType>::lchild_leaf(BTnode *leaf,BTnode *subtree)
{
    while(subtree->_lchild)
        subtree = subtree->_lchild;
    subtree->_lchild = leaf;
}

//根节点的移除
template<typename elemType>
void BinaryTree<elemType>::remove_root()
{
    if(!_root) return;
    BTnode<elemType>*tmp = _root;
    if(_root->_rchild)
    {
        //右子节点设为根节点
        _root = _root->_rchild;
        //将左子节点搬移到右子节点的左子树底部
        if(tmp->_lchild)
        {
            BTnode<elemType>*lc = tmp->_lchild;//原左子节点
            BTnode<elemType>*newlc = _root->_lchild;//原右子树的左子节点
            if(!newlc)
            {
                //没有左子节点，
                _root->_lchild = lc;
            }
            else
            {
                BTnode<elemType>::lchild_leaf(lc,newlc);
            }
        }
    }
    else _root = _root->_lchild;
    delete tmp;
}

//其他节点的移除,remove_value()拥有两个参数：将被删除的值以及一个指向目前关注的节点的父节点的指针
template<typename valType>
void BTnode<valType>::remove_value(const valType &val,BTnode *&prev)
{
    if(val<_val)
    {
        if(!_lchild)
            return;
        else
            _lchild->remove(val,_lchild);
    }
    else if(val>_val)
    {
        if(!_rchild)
            return;
        else
            _rchild->remove_value(val,_rchild);
    }
    //找到了，重置此树，并删除这一节点
    else
    {
        if(_rchild)
        {
            prev = _rchild;
            if(_lchild)
            {
                if(!prev->_lchild)
                    prev->_lchild = _lchild;
                else
                    BTnode<valType>::lchild_leaf(_lchild,prev->lchild);
            }
        }
        else prev = _lchild;
        delete this;
    }   
}
```

**移除整颗二叉树**

```c++
template<typename elemType>
void BinaryTree<elemType>::clear(BTnode<elemType>*pt)
{
    if(pt)
    {
        clear(pt->_lchild);
        clear(pt->_rchild);
        delete pt;
    }
}
```

**遍历算法**

```c++
//前序遍历
template<typename valType>
void BTnode<valType>::preorder(BTnode *pt,ostream &os)const
{
    if(pt)
    {
        display_val(pt,os);
        if(pt->_lchild) preorder(pt->_lchild,os);
        if(pt->_rchild) preorder(pt->_rchild,os);
    }
}

//中序遍历
template<typename valType>
void BTnode<valType>::inorder(BTnode *pt,ostream &os)const
{
    if(pt)
    {
        if(pt->_lchild) inorder(_lchild,os);
        display_val(pt,os);
        if(pt->_rchild) inorder(_rchild,os);
    }
}

//后序遍历
template<typename valType>
void BTnode<valType>::postorder(BTnode *pt,ostream &os)const
{
    if(pt)
    {
        if(pt->_lchild) postorder(pt->_lchild,os);
        if(pt->_rchild) postorder(pt->_rchild,os);
        display_val(pt,os);
    }
}
```

## 6.5 一个以Function Template完成的Output运算符

```c++
template<typename valType>
inline ostream& operator<<(ostream &os,const BinaryTree<elemType> &bt)
{
    os<<"Tree: "<<endl;
    bt.print(os);
    return os;
}
```

## 6.6 常量表达式与默认参数值

可以用常量表达式作为template的参数，将“对象所含的元素个数”参数化。

```c++
template<int len,int beg_pos=1>

Fibonacci<16>fib;
```

全局作用域内的函数及对象，其地址也是一种常量表达式，因此也可以被拿来表达这一形式的参数。

```c++
template<void (*pf)(int pos,vector<int>&seq)>
class numeric_sequence
{
public:
    numeric_sequence(int len,int geb_pos=1)
    {
        if(!pf)
            //产生错误信息并退出
        _len = len>0?len:1;
        _beg_pos = beg_pos>0?beg_pos:1;
        
        pf(beg_pos+len-1,_elems);
    }
private:
    int _len;
    int _beg_pos;
    vector<int>_elems;
};
```

```c++
//pf是一个指向“依据特定数列类型，产生pos个元素，放到vector seq内”的函数
//用法如下：
void fibonacci(int pos,vector<int>&seq);
void pell(int pos,vector<int>&seq);

numeric_sequence<fibonacci> ns_fib(12);
numeric_sequence<pell> ns_pell(18,8);
```

## 6.7 以Template参数作为一种设计策略

```c++
template<tyename elemType,typename Comp = less<elemType> >
class LessThanPred{
public:
    LessThanPred(const elemType &val):_val(val){}
    bool operator()(const elemType &val)const
    {
        return Comp(val,_val);
    }
    void val(const elemType &newval){_val = newval;}
    elemType val()const {return _val;}
private:
    elemType _val;
};

class StringLen{
public:
    bool operator()(const string &s1,const string &s2)
    {
        return s1.size()<s2.size();
    }
};

//应用
LessThanPred<int> ltpi(1024);
LessThanPred<int,StringLen> ltps("Pooh");
```

## 6.8 Member Template Function

```c++
class PrintIt{
public:
    PrintIt(ostream &os):_os(os){}
    //成员模板函数
    template<typename elemType>
    void print(const elemType &elem,char delimiter = '\n')
    {
        _os<<elem<<delimiter;
    }
private:
    ostream& _os;
};

int main()
{
    PrintIt to_standard_out(cout);
    to_standard_out.print("hello");
    to_standard_out.print(1024);
    
    string my_string("i am a string");
    to_standard_out.print(my_string);
}
```

# 第7章 异常处理

## 7.1 抛出异常

异常处理机制：异常的鉴定与发出、异常的处理方式。

异常出现之后，程序被暂停，异常处理机制开始搜索程序中有能力处理这一异常的地点，异常被处理完毕之后，程序的执行便会继续，从异常处理点接着执行下去。

C++通过throw表达式产生异常，throw表达式类似函数调用

```c++
inline void Triangular_iterator::check_interity()
{
    if(_index>=Triangular::_max_elems)
        throw iterator_overflow(index,Triangular::_max_elems);
    if(_index>=Triangular::_elems.size())
        Triangular::gen_elements(_index+1);
}

class iterator_overflow{
public:
    iterator_overflow(int index,int max):_index(index),_max(max){}
    int index(){return _index;}
    int max(){return _max;}
    
    void what_happened(ostream &os=cerr)
    {
        os<<"Internal error current index "
            <<_index<<" exceeds maximum bound: "<<_max;
    }
private:
    int _index;
    int _max;
};
```

## 7.2 捕获异常

利用单条或一连串的catch子句来捕获(catch)被抛出的异常。catch子句由三部分组成：关键字catch、小括号内的一个类型或对象、大括号内的一组语句（用来处理异常）。

```c++
extern void log_message(const char*);
extern string err_messages[];
extern ostream log_file;

bool some_function()
{
    bool status = true;
    
    catch(int errno)
    {
        log_message(err_messages[errno]);
        status = false;
    }
    catch(const char *str)
    {
        log_message(str);
        status = false;
    }
    catch(iterator_overflow &iof)
    {
        iof.what_happened(log_file);
        status = false;
    }
    return status;
}
```

上述语句可以处理前一节所抛出的三个异常对象：

```c++
throw 42;
throw "panic:no buffer!";
throw iterator_overflow(_index,Triangular::_max_elems);
```

有时候我们无法完成异常的完整处理，需要重新抛出异常，寻求其它catch子句的协助：

```c++
catch(iterator_overflow &iof)
{
    log_message(iof.what_happened());
    //重新抛出异常，令另一个catch子句接手处理
    throw; //重新抛出时，只需写下throw关键字即可
}
```

如果想捕获任何类型的异常，在异常声明部分指定省略号即可：

```c++
catch(...)
{
    log_message("exception of unknown type");
    //清理(clean up)然后退出
}
```

## 7.3 提炼异常

catch子句和try块一起使用。try块以关键字try作为开始，然后是大括号内的一连串程序语句。catch子句放在try块的末尾，这表示如果try块内有任何异常发生，便由接下来的catch子句加以处理。C++规定每一个异常都应该被处理。

```c++
bool has_elem(Triangular_iterator first,Triangular_iterator last,int elem)
{
    bool status = true;
    try
    {
        while(first!=last)
        {
            if(*first==elem)
                return;
            ++first;
        }
    }
    //捕获异常
    catch(iterator_overflow &iof)
    {
        log_message(iof.what_happened());
        log_message("check if iterator address same container");
    }
    
    status = false;
    return status;
}
```

## 7.5 标准异常

如果new表达式无法从程序的空闲空间分配到足够的内存，它会抛出bad_alloc异常对象。

```c++
vector<string>* init_text_vector(ifstream &infile)
{
    vector<string>*ptext = 0;
    try{
        ptext = new vector<string>;
        
    }
    catch(bad_alloc){
        cerr<<"ouch.headp memory exhausted!\n";
    }
    return ptext;
}
```

标准库定义了一套异常类体系，其根部是名为exception的抽象基类。exception声明有一个what()虚函数，会返回一个const char*，来表示被抛出异常的文字描述。

将自己编写的iterator_overflow继承于exception基类之下，并提供自己的what()。

```c++
#include<exception>

class iterator_overflow : public exception{
public:
    iterator_overflow(int index,int max):_index(index),_max(max){}
    int index(){return _index;}
    int max(){return _max;}
    
    const char* what()const;
private:
    int _index;
    int _max;
};
```

```c++
#include<sstream>  //使用ostringstream class
#include<string>

const char* iterator_overflow::what()const
{
    ostringstream ex_msg;//可以将int等对象转化为对应的字符串
    static string msg;
    
    ex_msg<<"Internal error: current index "<<_index<<" exceeds maxium bound: "<<_max;
    
    //萃取出string对象
    msg = ex_msg.str();
    
    //萃取出const char*表达式
    return msg.c_str();; //将string转化为C-Style字符串
}
```





























