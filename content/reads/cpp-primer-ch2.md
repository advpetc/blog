---
title: "Cpp Primer Chapter 2"
date: 2021-05-26T10:08:12-05:00
draft: false
author: "Peter"
categories: ["book"]
tags: ["cpp", "cpp primer"]
---

## C++ language specifics

Python check types at run time, whereas C++ is statically typed language — type-check is done at compile time. As a consequence, the compiler must know the type of every name used in the program.

## Primitive Buit-in Types

C++ defines a set of primitive types that include the **arithmetic types** and a special type named **void**. The arithmetic types represent characters, integers, boolean values, and floating-point numbers. The void type has no associated values and can be used in only a few circumstances, most commonly as the return type for functions that do not return a value.

### Arithmetic Types

1. bool: boolean (NA)
2. char: character (8 bits)
3. wchar_t: wide character (16 bits)
4. char16_t:  Unicode character (16 bits)
5. char32_t:  Unicode character (32 bits)
6. short: short integer (16 bits)
7. int:  integer (16 bits)
8. long:  long integer (32 bits)
9. long long: long integer (64 bits)
10. float: single-precision floating-point (6 significant digits)
11. double: double-precision floating-point (10 significant digits)
12. long double: extended-precision floating-point (10 significant digits)



Typically, `floats` are represented in one word (32 bits), `doubles` in two words (64 bits), and `long double` in either three or four words (96 or 128 bits). The `float` and `double` types typically yield about 7 and 16 significant digits, respectively.



Except for bool and the extended character types, the integral types may be **signed** or **unsigned**. A signed type represents negative or positive numbers (including zero); an unsigned type represents only values greater than or equal to zero .The type `unsigned int` may be abbreviated as `unsigned`.



### A short note to use `char`

Do not use plain `char` or `bool` in arithmetic expression. Use them *only* to hold characters or truth values. Computations using `char` are especially problematic because `char` is `signed` on some machine and `unsigned` on others. If you need a tiny integer, explicitly specify either `signed char` or `unsigned char`. 



## Type conversion

```c++
bool b = 42; // b is true: anything not 0 is true
int i = b;   // i has value 1
i = 3.14;    // i has value 3: cast down
double pi = i; // pi has value 3.0
unsigned char c = -1;  // assuming 8-bit chars, c has value 255: there is no negative for unsigned
signed char c2 = 256; // assuming 8-bit chars, the value of c2 is undefined
```

### Why `unsigned char c = -1` will get 255?

If we assign an out-of-range value to an object of unsigned type, the result is the remainder of the value modulo the number of values the target type can hold. For example, an 8-bit unsigned char can hold values from 0 through 255, inclusive. If we assign a value outside this range, the compiler assigns the remainder of that value **modulo** 256. Therefore, assigning –1 to an 8-bit unsigned char gives that object the value 255. Note that modulo mentioned here is **logical, arithmetic modulo operation**:

```
a mod n is a/n = r (remainder)
therefore, a mod n = a - r * n
so, -1 mod 256 is
-1/256 = 1
=> -1 - (-1) * 256
=> 255
```

Reference: https://www.calculators.org/math/modulo.php

### Don't use `unsigned` in loop

```c++
for (int i = 10; i >= 0; --i)
  std::cout << i << std::endl;
```

We might think we could rewrite this loop using an unsigned. After all, we don’t plan to print negative numbers. However, this simple change in type means that our loop will never terminate:

```c++
for (unsigned u = 10; u >= 0; --u)
	std::cout << u << std::endl;
```

`u` can never be less than 0; the condition will always succeed. Consider what happens when `u` is 0. On that iteration, we’ll print 0 and then execute the expression in the for loop. That expression, `—u`, subtracts 1 from `u`. That result, -1, won’t fit in an unsigned value. As with any other out-of-range value, -1 will be transformed to an unsigned value. Assuming 32-bit ints, the result of `—u`, when `u` is 0, is 4294967295.

## Literal

If we write what appears to be a negative decimal literal, for example, `-42`, the minus sign is *not* part of the literal. The minus sign is an **operator** that negates the value of its (literal) operand.



The type of a string literal is **array** of constant chars:

* `'a'`: character literal
* `"hello world"`: string literal

Compiler will append a null character ('\0') at the end of every string literal, so the length is always string literal size + 1.



### Specify the Type of a Literal

We can override the default type of an integer, floating-point, or character literal by supplying a suffix or prefix as listed below:

```c++
L'a' // wide character literal, type is wchar_t
u8"hi!" // utf-8 string literal (utf-8 encodes a Unicode character in 8 bits)
42ULL // unsigned integer literal, type is unsigned long long 
1E-3F // single-precision floating-point literal, type is float 
3.14159L // extended-precision floating-point literal, type is long double
```

### Default Initialization

Uninitialized objects of built-in type defined inside a function body have undefined value. However, the library string class says that if we do not supply an initializer, then the resulting string is the empty string.

## Variable

```c++
int units_sold = {0};
int units_sold{0};
```

The use of curly braces to initialize a variable is called **List Initialization**. The compiler will not let us list initialize variables of built-in type if the initializer might lead to the loss of information:

```c++
long double ld = 3.1415;
int a{ld}, b = {ld}; // error: narrowing conversion required
int c(ld), d = ld;   // ok: not using list init but the value will be truncated
```

### Reference

When we define a reference, instead of copying the ini- tializer’s value, we **bind** the reference to its initializer.

```c++
int ival = 1024;
int &refVal = ival;
cout << refVal << endl; // 1024
refVal = 2;
cout << ival << endl; // 2
```

When we assign to a reference, we are assigning to the object to which the reference is bound. When we fetch the value of a reference, we are really fetching the value of the object to which the reference is bound. The type of a reference and the object to which the reference refers must match exactly

```c++
int &refVal4 = 10; // error: initializer must be an object
double dval = 3.14;
int &refVal5=dval;// error: initializer must be an int object
int &refVal6; // error: a reference must be initialized
```

## Pointers

A pointer holds the address of another object. We get the address of an object by using the address-of operator (the **&** **operator**):

```c++
int ival = 42;
int *p = &ival; // p holds the address of ival; p is a pointer to ival
```

Because references are **not objects**, they don’t have addresses. Hence, we may not define a pointer to a reference.

### Using a Pointer to Access an Object

When a pointer points to an object, we can use the dereference operator (the * **operator**) to access that object:

```c++
int ival = 42;
int *p = &ival; // p holds the address of ival; p is a pointer to ival 
int *p2 = ival; // illegal to assign an int variable to a pointer
cout << *p; // * yields the object to which p points; prints 42
```

Dereferencing a pointer yields the object to which the pointer points. We can assign to that object by assigning to the result of the dereference:

```c++
*p = 0; // * yields the object; we assign a new value to ival through p 
cout << *p;// prints 0

p = &ival; // it will pointing to ival's address
```

`*` can have multiple meanings:

1. After type: `int *p` means `p` is a pointer, and it will point to an address
2. Everything else: `p = &ival` means `p` will point to an object with a referece but `p` is already declared

### Null Pointers

Older programs sometimes use a **preprocessor variable** named NULL, which the `cstdlib` header defines as 0. Modern C++ programs generally should avoid using NULL and use nullptr instead.

### `void*` Pointers

The type **void\*** is a special pointer type that can hold the address of any object. Like any other pointer, a void* pointer holds an address, but the type of the object at that address is unknown:

```c++
double obj = 3.14, *pd = &obj;
// ok: void* can hold the address value of any data pointer type 
void *pv = &obj; // obj can be an object of any type 
pv = pd; // pv can hold a pointer to any type
```

### Difference between Pointers and References

#### key difference:

1. a reference is another name of an **already existing** object. a pointer is an object in its **own right**.
2. Once initialized, a reference remains **bound to** its initial object. There is **no way** to rebind a reference to refer to a different object. a pointer can be **assigned** and **copied**.
3. a reference always get the object to which the reference was initially bound. a single pointer can point to **several different objects** over its lifetime.
4. a reference must be initialized. a pointer need **not be** initialized at the time it is defined.

## `const` Qualifier

By default, `const` Objects are local to a file. Sometimes we have a const variable that we want to share across multiple files but whose initializer is not a constant expression. In this case, we don’t want the compiler to generate a separate variable in each file. Instead, we want the const object to behave like other (nonconst) variables. We want to define the const in one file, and declare it in the other files that use that object.

To define a single instance of a const variable, we use the keyword extern on both its definition and declaration(s):

```c++
// file_1.cc defines and initializes a const that is accessible to other files 
extern const int bufSize = fcn();
// file_1.h
extern const int bufSize;// same bufSize as defined in file_1.cc
```

 To share a `const` object among multiple files, you must define the variable as `extern`.

### `const` and Pointers

Like a reference to const, a **pointer to** **const** may not be used to change the object to which the pointer points. We may store the address of a const object only in a pointer to const:

```c++
const double pi = 3.14; // pi is const; its value may not be changed 
double *ptr = &pi; // error:ptr is a plain pointer 
const double *cptr = &pi; // ok:cptr may point to a double that is const 
*cptr = 42; // error: cannot assign to *cptr
```

### `const` pointer

For example:

```c++
int errNumb = 0;
int *const curErr = &errNumb; // curErr will always point to errNumb 
const double pi = 3.14159;
const double *const pip = &pi; // pip is a const pointer to a const object
```

In this case, the symbol closest to `curErr` is const, which means that `curErr` itself will be a const object. The next symbol in the declarator is *, which means that `curErr` is a const pointer. Finally, the base type of the declaration completes the type of `curErr`, which is a const pointer to an object of type int. Similarly, pip is a const pointer to an object of type const double.

Neither the value of the object addressed by `pip` nor the address stored in `pip` can be changed. On the other hand, `curErr` addresses a plain, nonconst int. You cannot make it point to something else.

> Use const pointer to reference the same address in hardware: https://stackoverflow.com/a/219956

We use the term **top-level** **const** to indicate that the pointer itself is a const. When a pointer can point to a const object, we refer to that const as a **low-level** **const**. In general, a pointer is usually a **lower-level const**, and the others are **top-level const**. 

```c++
int i = 0;
int *const p1 = &i; // we can't change the value of p1; const is top-level
const int c1 = 42; // we cannot change ci; const is top-level
const int *p2 = &ci; // we can change p2; const is low-level
const int *const p3 = p2; // right-most const is top-level, left-most is not
const int &r = ci; // const in reference types is always low-level
```

### `constexpr` and Constant Expression

Only **literal** types can be declared by `constexpr`: arithmetic, reference, and pointer types are literal types. Variables defined inside a function are not stored at a fixed adress, so we cannot use a `constexpr` pointer to point to such variables. On the other hand, the address of an object defined outside of any function is a constant expression, and so may be used to initialize a constexpr pointer. 

## Types

There are two ways to define type aliasing:

1. via `typedef`: `typedef double wages;` — wages is a synonym for `double`
2. via **alias declaration**: `using SI = Sales_item;` where `Sale_item` is a type, and we use `using` keyword.

### Difference between `decltype` and `auto`

1. `decltype` is the *only* context in which a variable defined as a reference is not treated as a synonym for the object to which it refers.
2. `decltype` needs to deal with parentheses: `decltype((i)) d;` error: `d` is `int&` and must be initialized; `decltype(i) e;` ok: `e` is an (uninitialized) `int`

## Defining Our Own Data Structures

Defining our `Sales_data` class:

```c++
// Sale_data.h
struct Sales_data {
  std::string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};
```

> Don't forget the semicolumn at the end of the `struct` definition

Revisit chapter 1 and we know that each input contains:

```
0-201-78345-X 3 20.00
0-201-78345-X 2 25.00
```

An ISBN, the count of how many books were sold, and the price at which each book was sold.

### Read and add two books

Completed code (exercise 2.41):

#### Read single book

```c++
#include <iostream>
#include <string>

struct Sale_data {
  std::string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};

int main() {
  Sale_data book;
  double price;
  std::cin >> book.bookNo >> book.units_sold >> price;
  book.revenue = book.units_sold * price;
  std::cout << book.bookNo << " " << book.units_sold << " " << book.revenue
            << " " << price;

  return 0;
}
```

#### Read two books and add them together

```c++
#include <iostream>
#include <string>

struct Sale_data {
  std::string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};

int main() {
  Sale_data book1, book2;
  double price1, price2;
  std::cin >> book1.bookNo >> book1.units_sold >> price1;
  std::cin >> book2.bookNo >> book2.units_sold >> price2;
  book1.revenue = book1.units_sold * price1;
  book2.revenue = book2.units_sold * price2;

  if (book1.bookNo == book2.bookNo) {
    unsigned totalCnt = book1.units_sold + book2.units_sold;
    double totalRevenue = book1.revenue + book2.revenue;
    std::cout << book1.bookNo << " " << totalCnt << " " << totalRevenue << " ";
    if (totalCnt != 0)
      std::cout << totalRevenue / totalCnt << std::endl;
    else
      std::cout << "(no sales)" << std::endl;
    return 0;
  } else {
    std::cerr << "Data must refer to same ISBN" << std::endl;
    return -1;  // indicate failure
  }
}
```



#### Read multiple books and calculate the revenue

```c++
#include <iostream>
#include <string>

struct Sale_data {
  std::string bookNo;
  unsigned units_sold = 0;
  double revenue = 0.0;
};

int main() {
  Sale_data total;
  double totalPrice;
  if (std::cin >> total.bookNo >> total.units_sold >> totalPrice) {
    total.revenue = total.units_sold * totalPrice;

    Sale_data trans;
    double transPrice;
    while (std::cin >> trans.bookNo >> trans.units_sold >> transPrice) {
      trans.revenue = trans.units_sold * transPrice;

      if (total.bookNo == trans.bookNo) { // same book
        total.units_sold += trans.units_sold;
        total.revenue += trans.revenue;
      } else { // start calculating new book
        std::cout << total.bookNo << " " << total.units_sold << " "
                  << total.revenue << " ";
        if (total.units_sold != 0) // calculate the average revenue
          std::cout << total.revenue / total.units_sold << std::endl;
        else
          std::cout << "(no sales)" << std::endl;

        total.bookNo = trans.bookNo;
        total.units_sold = trans.units_sold;
        total.revenue = trans.revenue;
      }
    }

    std::cout << total.bookNo << " " << total.units_sold << " " << total.revenue
              << " ";
    if (total.units_sold != 0)
      std::cout << total.revenue / total.units_sold << std::endl;
    else
      std::cout << "(no sales)" << std::endl;

    return 0;
  } else {
    std::cerr << "No data?!" << std::endl;
    return -1;  // indicate failure
  }
}

```

This program must ensure the input is sorted by `bookNo`.

## Header files

Some note for writing header files:

1. The program that uses the header file will use the already included library (string.h for example).
2. Whenever a header is updated, the source files that use that header must be recompiled to get the new or changed declarations.

## Terms

1. **Escape sequence**: some characters, such as backspace or control characters, have no visible image

   1. newline: \n
   2. vertical tab: \v
   3. baskslash: \\
   4. carriage return: \r
   5. horizontal tab: \t
   6. backspace: \b
   7. question mark: \?
   8. formfeed: \f
   9. alert (bell): \a
   10. double quote: \\"
   11. single quote: \\'

2. **Separate compilation**: To allow programs to be written in logical parts, C++ supports what is commonly known as **separate compilation**. Separate compilation lets us split our programs into several files, each of which can be compiled independently.

   * A variable **declaration** specifies the type and name of a variable. A variable **definition** is a declaration. In addition to specifying the name and type, a definition also allocates storage and may provide the variable with an initial value.

   * To use a variable in more than one file requires **declarations** that are separate from the variable’s **definition**. To use the same variable in multiple files, we must define that variable in one—and only one—file. Other files that use that variable must **declare**—but not **define**—that variable.

   * ```c++
     extern int ix = 1024; // definition
     int iy; // definition
     extern int iz; // declaration
     ```

3. **Preprocessor**: The preprocessor — which C++ inherits from C — is a program that runs before the compiler and changes the source of text of our programs. C++ programs also use the preprocessor to define **header guards**. Preprocessor variables have one of two possible states: defined or not defined. The **#define** directive takes a name and defines that name as a preprocessor variable. There are two other direc- tives that test whether a given preprocessor variable has or has not been defined: **#ifdef** is true if the variable has been defined, and **#ifndef** is true if the variable has *not* been defined. If the test is true, then everything following the #ifdef or #ifndef is processed up to the matching **#endif**.

