---
title: "Cpp Primer Chapter 3"
date: 2021-05-30T10:14:07-05:00
draft: true
author: "Peter"
categories: ["book"]
tags: ["cpp", "cpp primer"]
---

## Namespace `using` Declarations

A using declaration lets us use a name from a namespace without qualifying the name with a namespace_name::prefix. A using declaration has the form:

`using namespace::name;`

For example:

```c++
#include <iostream>
using std::cin;
int main() {
  int i;
  cin >> i; // ok: cin is a synonym for std::cin
  cout << i; // error: no using declaration
  std::cout << i; // ok
  return 0;
}
```

Code inside headers ordinarily **should not** use `using` declarations. The reason is that the contents of a header are copied into the including program’s text. If a header has a `using` declaration, then every program that includes that header gets that same using declaration. As a result, a program that didn’t intend to use the specified library name might encounter unexpected name conflicts.

## Library `string` Type

We can define a string in following ways:

```c++
string s1; // ""
string s2 = s2; // copy
string s3 = "hiya"; // literal
string s4(10, 'c'); // "cccccccccc"
string s5("hiya"); // direct init
string s6(s2); // s6 is a copy of s2
```

If we use `=`, then we are doing **copy initialization**. If we don't use `=`, like s4, we are using **direct initializaiton**. 

### Read in `string`

#### Reading an unknown number of strings:

```c++
int main() {
  string word;
  while (cin >> word) // read unitl end-of-file
    cout << word << "+" << endl;  
  return 0;
}
```

This will read until the end of line, and delimited by space or newline. 

Given input:

```
a c
b

c
```

Ouptut:

```
a+
c+
b+
c+
```



#### Using `getline` to read an entire line:

```c++
int main() {
  string line;
  // read input a line at a time until end-of-file 
  while (getline(cin, line))
  	cout << line << "+" << endl;
  return 0;
}
```

This will read until there is a new line, and the space in each line remains.

Output (with previous input):

```
a c+
b+
+
c+
```

### Adding Literals and `strings`

When we mix strings and string or character literals, at least one operand to each + operator must be of string type: `string s = "hello" + "world!";` is wrong, because there is no `string` operand. `string s = "hello" + ", " + s2;` is also wrong, since there is no `string` operand between `"hello"` and `","`.



 How whitespace characters are handled in the string input operator and in the `getline` function.

- For code like `is >> s`, input is separated by whitespaces while reading into string `s`.
- For code like `getline(is, s)` input is separated by newline `\n` while reading into string `s`. Other whitespaces are ignored.
- For code like `getline(is, s, delim)`input is separated by `delim` while reading into string `s`. All whitespaces are ignored.

## Library `vector` Type

A vector is a **class template**. C++ has both class and function templates. Templates are not themselves functions or classes. Instead, they can be thought of as instructions to the compiler for generating classes or functions. The process that the compiler uses to create classes or functions from templates is called **instantiation**. When we use a template, we specify what kind of class or function we want the compiler to instantiate. We supply it inside a pair of angle brackets following the template’s name: `#include <vector>;`

Because references are not objects, we cannot have a `vector` of references.

### **Defining and Initializing** `vector`

```c++
vector<int> ivect; // initially empty
vector<int> ivec2(ivect); // copy elements of ivec to ivec2
vector<int> ivec3 = ivec; // copy elements of ivec to ivec3
vector<string> articles = {"a", "an", "the"}; // with string literal
```

We cannot supply a list of initializers using parentheses:

```c++
vector<string> v1{"a", "an", "the"}; // ok: using curly braces
vector<string> v2("a", "an", "the"); // error: cannot use parentheses
vector<string> v3{10, "hi"}; // equal to v3(10, "hi");
```

When we use parentheses, we are saying that the values we supply are to be used to *construct* the object. When we use curly braces, {...}, we’re saying that, if possible, we want to *list initialize* the object.

## Introducing Iterators

In the case of an iterator, that object is an element in a container or a character in a string. We can use an iterator to fetch an element and iterators have operations to **move** from one element to another. As with pointers, an iterator may be valid or invalid. A valid iterator either denotes an **element** or denotes a **position** one past the last element in a container. All other iterator values are invalid.

### Using iterators

Unlike pointers, we do not use the address-of operator to obtain an iterator. In- stead, types that have iterators have members that return iterators. In particular, these types have members named **begin** and **end**. The begin member returns an iterator that denotes the first element (or first character), if there is one:

```c++
auto b = v.begin(), e = v.end(); // b and e are the same type
```

The iterator returned by end is an iterator positioned “one past the end” of the associated container (or string). This iterator denotes a **nonexistent** element “off the end” of the container. It is used as a marker indicating when we have processed all the elements. The iterator returned by end is often referred to as the **off-the-end iterator** or abbreviated as “the end iterator.” If the container is empty, begin returns the same iterator as the one returned by end.

As with pointers, we can dereference an iterator to obtain the element denoted by an iterator:

```c++
string s("some string");
if (s.begin() != s.end()) { // make sure s is not empty
  auto it = s.begin();
  *it = toupper(*it);
}
```

### Common Iterators Operations

![Imgur](https://i.imgur.com/V86ywPp.png)

To let us ask specifically for the `const_iterator` type, the new standard introduced two new functions named `cbegin` and `cend`:

```c++
vector<int> v;
auto it = v.cbegin(); // it has type vector<int>::const_iterator
```

To simplify the operation of `(*it).empty()`, c++ introduces a shortcut `it -> empty()`, and the arrow sign can be applied directly to pointer.

### Using Iterator Arithmetic

Using iterator for binary search:

```c++
auto beg = text.begin(), end = text.end();
auto mid = text.begin() + (end - beg) / 2;
while (mid != end && *mid != sought) {
  if (sought < *mid) 
    end = mid;
  else
    beg = mid + 1;
  mid = beg + (end - beg) / 2;
}
```

## Arrays

An array is a data structure that is similar to the library vector type but offers a different trade-off between performance and flexibility. Like a vector, an array is a container of unnamed objects of a single type that we access by position. Unlike a vector, arrays have fixed size; we cannot add elements to an array. Because arrays have fixed size, they sometimes offer better run-time performance for specialized applications. However, that run-time advantage comes at the cost of lost flexibility.

### Defining and Initializing Build-in Arrays

It requires a `constexpr` to define an array's size:

```c++
unsigned cnt = 42;
constexpr unsigned sz = 42;
int arr[10]; // ok
int *parr[sz]; // ok: array of int pointer
string bad[cnt]; // error: cnt isn't constant expression
string strs[get_size()]; // ok if get_size() return a constexpr
```

Define an array with literals:

```c++
const unsigned sz = 3;
int al[sz] = {0, 1, 2};
int a2[] = {0, 1, 2};
int a3[5] = {0, 1, 2}; // ok: equivalent to a3[] = {0, 1, 2, 0, 0};
string a4[3] = {"hi", "bye"}; // ok: equivalent to a4[] = {"hi", "bye", ""};
int a5[2] = {0, 1, 2}; // error: to many initializers
```

Since String is an array of char, it has its own way of definition:

```c++
char a1[] = {'C', '+', '+'}; // list init, no null
char a2[] = {'C', '+', '+', '\0'}; // list init, explicit null
char a3[] = "C++"; // null terminator added automatically
const char a4[6] = "Daniel"; // error: no space for the null
```

We cannot initialize an array as a copy of another array, nor is it legal to assign one array to another:

```c++
int a[] = {0, 1, 2}; // array of three ints
int a2[] = a; // error: cannot initialize one array with another 
a2 = a; // error: cannot assign one array to another
```

Default values when initialize:

```c++
string sa[10];      //all elements are empty strings
int ia[10];         //all elements are 0

int main() {
    string sa2[10]; //all elements are empty strings
    int ia2[10];    //all elements are undefined
}
```

### Pointers and Arrays

Arrays have a special property—in most places when we use an array, the compiler automatically substitutes a pointer to the first element:

```c++
string *p2 = nums; // equivalent to p2 = &nums[0]
```

#### Pointers are iterators

Using pointers to write a loop to print the elements in `arr` as follows:

```c++
int arr[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
int *p = arr; // pointing at the begining of the arr
++p; // pointing to the second element: for demo purpose
int *e = &arr[10]; // pointing at the end of the arr
for (int *b = arr; b != e; ++b) {
  cout << *b << endl;
}
```

The libray `begin()` and `end()` functions

```c++
int ia[] = {0,1,2,3,4,5,6,7,8,9}; // ia is an array of ten ints 
int *beg = begin(ia); // pointer to the first element in ia
int *last = end(ia); // pointer one past the last element in ia
```

`begin` returns a pointer to the first, and `end` returns a pointer one past the last element in the given array: These functions are defined in the `iterator` header.

So we can use these two library function to iterate the `arr`:

```c++
// pbeg points to the first and pend points just past the last element in arr 
int *pbeg = begin(arr), *pend = end(arr);
// find the first negative element, stopping if we’ve seen all the elements 
while (pbeg != pend && *pbeg >= 0)
	++pbeg;
```

The result of subtracting two pointers is a library type named **ptrdiff_t**. Like size_t, the ptrdiff_t type is a machine-specific type and is defined in the cstddef header. Because subtraction might yield a negative distance, ptrdiff_t is a signed integral type.

```c++
auto n = end(arr) - begin(arr);
```

Comparing two vectors:

```c++
vector<int> vec1 = { 0, 1, 2 };
vector<int> vec2 = { 0, 1, 2 };

if (vec1 == vec2) // will compare all the values in two vectors
  cout << "The two vectors are equal." << endl;
else
  cout << "The two vectors are not equal." << endl;
```

### C library String Functions

The Standard C library provides a set of functions, listed in Table 3.8, that operate on C-style strings. These functions are defined in the cstring header, which is the C++ version of the C header string.h.

![Imgur](https://i.imgur.com/pTmoRb4.png)

An easy code snippet for comparing two C-style string:

```c
const char *cs1 = "abc";
const char *cs2 = "def";
auto result = strcmp(cs1, cs2);
if (result == 0)
  cout << "same strings" << endl;
else if (result < 0)
  cout << "abc < def" << endl;
else
  cout << "abc > def" << endl;
```

### Interfacing to Older Code

Many C++ programs predate the standard library and do not use the string and vector types. Moreover, many C++ programs interface to programs written in C or other languages that cannot use the C++ library. Hence, programs written in modern C++ may have to interface to code that uses arrays and/or C-style charac- ter strings. The C++ library offers facilities to make the interface easier to manage.

Transform a string to char array:

```c++
string s("hello world");
char *str = s; // error:can’t initialize a char* from a string
const char *str = s.c_str(); // ok
```

Transform any array to vector:

```c++
int int_arr[] = {0, 1, 2, 3, 4};
vector<int> ivec(begin(int_arr), end(int_arr));
// or just a subset of the array
vector<int> subVec(int_arr + 1, int_arr + 4);
```

## Multidimensional Arrays

### Initialization the elements of a multidimensional array

```c++
int ia[3][4] = {
  {0, 1, 2, 3},
  {4, 5, 6, 7},
  {8, 9, 10, 11}
};

// this is the same as the previous ia, but less readable
int ia[3][4] = {0,1,2,3,4,5,6,7,8,9,10,11}; 

int ia[3][4] = {{0}, {4}, {8}}; // we don't need to fill all the rows

// explicitly initialize row 0; the remaining elements are value initialized 
int ix[3][4] = {0, 3, 6, 9};
```

To use a multidimensional array in a range for, the loop control vari- able for all but the innermost array must be references.

```c++
// this will work, since row is a reference
for (const auto &row : ia)
  for (auto col : row)	
    cout << col << endl;

// this will not work, since row is a pointer
for (auto row : ia)
	for (auto col : row)
```



## Term

1. `std::size_type`: The string class—and most other library types—defines several compan- ion types. These companion types make it possible to use the library types in a machine-independent manner. The type **size_type** is one of these companion types. To use the size_type defined by string, we use the scope operator to say that the name size_type is defined in the string class. Although we don’t know the precise type of string::size_type, we do know that it is an `unsigned` type big enough to hold the size of any string.

2. `cctype` funtions: ![](https://i.imgur.com/nhe7SUz.png)

   

