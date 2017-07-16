Chapter 19 Specialized Tools and Techniques
===========================================

Section 19.1 Controlling Memory Allocation
------------------------------------------

>Exercise 19.1: Write your own `operator new(size_t)` function using `malloc` and use `free` to write the `operator delete(void*)` function.

The code page 823 just works.

>Exercise 19.2: By default, the `allocator` class uses `operator new` to obtain storage and `operator delete` to free it. Recompile and rerun your `StrVec` programs (§ 13.5, p. 526) using your versions of the functions from the previous exercise.

```cpp
// TODO:
```

Section 19.2 Run-Time Type Identification
-----------------------------------------

>Exercise 19.3: Given the following class hierarchy in which each class defines a `public` default constructor and virtual destructor:
>```cpp
>    class A { /* ... */ };
>    class B : public A { /* ... */ };
>    class C : public B { /* ... */ };
>    class D : public B, public A { /* ... */ };
>```
>which, if any, of the following `dynamic_cast`s fail?
>```cpp
>    (a) A *pa = new C;
>        B *pb = dynamic_cast<B*>(pa);
>    (b) B *pb = new B;
>        C *pc = dynamic_cast<C*>(pb);
>    (c) A *pa = new D;
>        B *pb = dynamic_cast<B*>(pa);
>```

(a) Success

(b) Fails

(c) Success. PS: by the definition of the code, there's ambiguous and it can be fixed by making class A inheritance declaration virtual.

> Exercise 19.4: Using the classes defined in the first exercise, rewrite the following code to convert the expression `*pa` to the type `C&`:
>```cpp
>    if (C *pc = dynamic_cast<C*>(pa)) {
>        // use C's members
>    } else {
>        // use A's members
>    }
>```

```cpp
    try {
        C& alias = dynamic_cast<C&>(*pa);
        // use C's members
    } catch (bad_cast) {
        // use A's members
    }
```

>Exercise 19.5: When should you use a `dynamic_cast` instead of a virtual function?

In the book it says `we can use one of the RTTI operators if we cannot use a virtual`. `virtual` is not possible for constructors and static functions...On page 829 the author gives an example where virtual functions is impossible: the virtual functions require exact the same parameter list in derived classes, and in a context of overriding operator equality function it's impossible.

>Exercise 19.6: Write an expression to dynamically cast a pointer to a `Query_base` to a pointer to an `AndQuery` (§ 15.9.1, p. 636). Test the cast by using objects of `AndQuery` and of another query type. Print a statement indicating whether the cast works and be sure that the ouput matches your expectations.

```cpp
    Query_base *bp = new AndQuery;
    if (typeid(*bp) == typeid(AndQuery)) {
        AndQuery *dp = dynamic_cast<AndQuery*>(bp);
        // ...
    }
```

>Exercise 19.7: Write the same cast, but cast a `Query_base` object to a reference to AndQuery. Repeat the test to ensure that your case works correctly.

```cpp
    Query_base &alias = new Query_base;
    if (typeid(alias) == typeid(AndQuery)) {
        AndQuery &and = dynamic_cast<AndQuery&>(alias);
        // ...
    } else {
        // ...
    }
```

>Exercise 19.8: Write a `typeid` expression to see whether two `Query_base` pointers point to the same type. Now check whether that type is an AndQuery.

```cpp
    bool result = typeid(*p1) == typeof(*p1) && typeid(*p1) == typeid(AndQuery);
```

>Exercise 19.9: Write aprogram similar to the last one in this section to print the names your compiler uses for common type names. If your compiler gives output similar to ours, write a function that will translate those strings to more human-friendly form.

```cpp
std::string demangle(const char* mangled)
{
    int status;
    std::unique_ptr<char[], decltype(std::free) *> result(
            abi::__cxa_demangle(mangled, 0, 0, &status), std::free);
    return result.get() ? std::string(result.get()) : "error occurred";
}

template<class T>
void foo(T&& t) {
    std::cout << "before: " << typeid(t).name() << std::endl;
    std::cout << "after : " << demangle(typeid(t).name()) << std::endl;
}

int main() {
    foo("f");            //char const*
    foo(std::string());  //std::string
}
```

Output is:
```
before: PKc
after : char const*
before: NSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE
after : std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >
```

>Exercise 19.10: Given the following class hierarchy in which each class defines a `public` default constructor and virtual destructor, which type name do the following statements print?
```cpp
    class A { /* ... */ };
    class B : public A { /* ... */ };
    class C : public B { /* ... */ };
    
    (a) A *pa = new C;
        cout << typeid(pa).name() << endl;
    (b) C cobj;
        A& ra = cobj;
        cout << typeid(&ra).name() << endl;
    (c) B *px = new B;
        A& ra = *px;
        cout << typeid(ra).name() << endl;
```

(a) A*

(b) A*

(c) B. PS: when I try to print the human-friendly type name with `foo` function template in the previous answer, it outputs `A` and it apparently lose the reference information when deducing the type. Fixed it by changing the function parameter type to `T&&`
