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
