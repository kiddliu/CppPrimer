Exception Handling
==================
Exercises Section 18.1.1
------------------------
>Exercise 18.1: What is the type of the exception object in the following `throw`s?
>```cpp   
>   (a) range_error r("error");
>       throw r;
>   (b) exception *p = &r;
>       throw *p;
>```
>What would happen if the throw in (b) were written as `throw p`?
The type in (a) is `range_error` and in (b) it's a `exception`. If in (b) it were written as `throw p`, then by catching `exception *e` it's a `runtime_error` that actually got caught instead of an `exception` that we're expecting.

>Exercise 18.2: Explain what happens if an exception occurs at the indicated point:
>```cpp
>   void exercise(int *b, int *e)
>   {
>       vector<int> v(b, e);
>       int *p = new int[v.size()];
>       ifstream in("ints");
>       // exception occurs here
>   }
>```
The array p points to could not be freed as it's allocated dynamically, then a memory leak occurs.

>Exercise 18.3: There are two ways to make the previous code work correctly if an exception is thrown. Describe them and implement them.

(a) with try...finally
```cpp
void exercise(int *b, int *e)
{
    vector<int> v(b, e);
    int *p = new int[v.size()];
    ifstream in("ints");
    
    try
    {
        // exception occurs here
    }
    finally
    {
        if (p) delete [] p;
    }
}
```
(b) with shared_ptr
```cpp
void exercise(int *b, int *e)
{
    vector<int> v(b, e);
    shared_ptr<int> p = shared_ptr<int>(new int[v.size()], [](int *p){ delete [] p; });
    ifstream in("ints");
    // exception occurs here
}
```
Exercises Section 18.1.2
------------------------
>Exercise 18.4: Looking ahead to the inheritance hierarchy in Figure 18.1 (p. 783), explain what's wrong with the following `try` block. Correct it.
>```cpp
>   try {
>       // use of the C++ standard library
>   } catch (exception) {
>       // ...
>   } catch (const runtime_error &re) {
>       // ...
>   } catch (overflow_error eobj) { /* ... */ }
>```

Quote
> Multiple `catch` clauses with types related by inheritance must be ordered from most derived type to least derived.

![std-overflow error-inheritance.svg](http://upload.cppreference.com/mwiki/images/b/b6/std-overflow_error-inheritance.svg "std-overflow error-inheritance")

As the image depicts, the last 2 `catch` clauses will never be matched. Reversing the order of `catch` clauses could fix the problem. 

>Exercise 18.5: Modify the following `main` function to catch any of the exception types show in Figure 18.1 (p. 783):
>```cpp
>   int main() {
>       // use of the C++ standard library
>   }
>```
> The handlers should print the error message associated with the exception before calling `abort` (defined in the header `cstdlib`) to terminate `main`.

```cpp
#include <cstdlib>

int main() {
    try {
        // use of the C++ standard library
    } catch (exception &e) {
        cerr << e.what() <<endl;
        abort();
    }
    
    return EXIT_SUCCESS;
}
```
>Exercise 18.6: Given the following exception types and `catch` clauses, write a `throw` expression that creates an exception object that can be caught by each `catch` clause:
>```cpp
>   (a) class exceptionType { };
>       catch(exceptionType *pet) { }
>   (b) catch(...) { }
>   (c) typedef int EXCPTYPE;
>       catch(EXCPTYPE) { }

```cpp
(a) throw new exceptionType();
(b) throw exception();
(c) throw 1;
```
Exercises Section 18.1.3
------------------------
>Exercise 18.7: Define your `Blob` and `BlobPtr` classes from Chapter 16 to use function `try` blocks for their constructors.

```
// TODO:
```
Exercises Section 18.1.4
------------------------
>Exercise 18.8: Review the classes you've written and add appropriate exception specifications to their constructors and desctructors. If you think one of your destructors might throw, change the code so that it cannot throw.

```
// TODO:
```
Exercises Section 18.1.4
------------------------
>Exercise 18.9: Define the bookstore exception classes described in this section and rewrite your `Sales_data` compound assignment operator to throw an exception.

The code on p. 783 and p. 784 are good to answer this question.

>Exercise 18.10: Write a program that uses the `Sales_data` addition operator on objects that have differing `ISBN`s. Write two versions of the program: one that handles the exception and one that does not. Compare the behavior of the programs so that you become familiar with what happens when an uncaught exception occurs.

```cpp
Sales_data &
accumulate(const Sales_data &first, const Sales_data &second)
{
    Sales_data result = first;
    result += second;
    
    return result;
}

Sales_data &
accumulate(const Sales_data &first, const Sales_data &second) noexcept
{
    Sales_data result = first;
    try {
        result += second;
    } catch (isbn_mismatch) {
        // if isbn mismatches, returns the first object
    }
    
    return result;
}
```

>Exercise 18.11: Why is it important that the `what` function doesn't throw?

Member function `what` usually is called in a `catch` block and if it throws an exception, the recovery procedure specified in this `catch` block breaks and usually it's not an expect behavior.
