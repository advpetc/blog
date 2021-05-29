---
title: "Cpp Primer Chapter 1"
date: 2021-05-24T21:36:54-05:00
draft: false
author: "Peter"
categories: ["book"]
tags: ["cpp", "cpp primer"]
---

Starting from today, I will read and write notes for C++ primer. I will only write notes that I think that is important, so not all the contents will be covered.

## Design a bookstore

Our store keeps a file of transactions, each of which records the sale of one or more copies of a single book. Each transaction contains three data elements:

```
     0-201-70353-X 4 24.99
```

The first element is an ISBN (International Standard Book Number, a unique book identifier), the second is the number of copies sold, and the last is the price at which each of these copies was sold. 

### `main`	

#### Original

```c++
#include <iostream>
#include "Sales_item.h"
int main() {
  Sales_item book;
  // read ISBN, number of copies sold, and sales price
  std::cin >> book;
  // write ISBN, number of copies sold, total revenue, and average price 
  std::cout << book << std::endl;
  return 0; 
}

```

#### Adding `Sales_items`

```c++
#include <iostream>
#include "Sales_item.h"
int main() {
  Sales_item item1, item2;
  std::cin >> item1 >> item2; // read a pair of transactions 
  std::cout << item1 + item2 << std::endl; // print their sum
  return 0; 
}
```

#### Completed `main`

```c++
#include <iostream>
#include "Sales_item.h"
int main() {
  Sales_item total; // variable to hold data for the next transaction
	// read the first transaction and ensure that there are data to process 
  if (std::cin >> total) {
		Sales_item trans; // variable to hold the running sum 
		// read and process the remaining transactions
		while (std::cin >> trans) { // if we’re still processing the same book
			if (total.isbn() == trans.isbn())
				total += trans; // update the running total 
      else { // print results for the previous book
        std::cout << total << std::endl;
        total = trans; // total now refers to the next book
      } 
    }
    std::cout << total << std::endl; // print the last transaction 
  } else { // no input! warn the user
    std::cerr << "No data?!" << std::endl; 
    return -1; // indicate failure
  }
  return 0; 
}

```

Only if the `isbn` of two books are the same can we add them together.

## Get the status code returned by `main()`

The value returned from main is accessed in a system-dependent manner. On both UNIX and Windows systems, after executing the program, you must issue an appropriate echo command.

On UNIX systems, we obtain the status by writing

```\$ echo $?```

To see the status on a Windows system, we write

```$ echo %ERRORLEVEL%```

## Exercise 1.16

Write your own version of a program that prints the sum of a set of integers read from `cin`.

```c++
#include <iostream>
int main()
{
    int sum = 0;
  	// read in until no input or empty input
    for (int val; std::cin >> val; sum += val);
    std::cout << sum << std::endl;

    return 0;
}
```

## Class

To use a class we need to know three things: 

* What is its name?
* Where is it defined?
* What operations does it support?

## Terms

1. From `std::cout << "Enter two numbers:"; std::cout << std::endl;`, the message is a **string literal**, The second operator prints endl, which is a special value called a **manipulator**.
2. The prefix `std::` indicates that the names `cout` and `endl` are defined inside the `namespace` named `std`. **Namespace** allows us to avoid inadvertent collision between the names we defined and uses of those same names inside a library.
3. Conventionally, **header file names** are derived from the name of a class defined in that header. Header files that we write usually have a suffix of .h, but some programmers use .H, .hpp, or .hxx.

