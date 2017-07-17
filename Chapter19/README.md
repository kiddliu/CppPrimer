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

Section 19.4 Pointer to Class Member
------------------------------------

>Exercise 19.11: What is the difference between an ordinary data pointer and a pointer to a data member?

Quote:
> Normally a pointer points to an object, but a pointer to member identifies a member of a class, not an object of that class. It embodies both the type of a class and the type of a member of that class.

>Exercise 19.12: Define a pointer to member that can point to the `cursor` member of class `Screen`. Fetch the value of `Screen::cursor` through that pointer.

```cpp
    const pos Screen::*pCursor = &Screen::cursor;
    Screen screen;
    pos cursor = screen.*pCursor;
```

>Exercise 19.13: Define the type that can represent a pointer to the `bookNo` member of the `Sales_data` class.

```cpp
typedef std::string Sales_data::* MemberPtr;
```

>Exercise 19.14: Is the following code legal? If so, what does it do? If not, why?
>```cpp
>    auto pmf = &Screen::get_cursor;
>    pmf = &Screen::get;
>```

Legal, cause the signature is exactly the same.

>Exercise 19.15: What is the difference between an ordinary function pointer and a pointer to a member function?

An ordinary pointer is just a pointer to a function, whereas a pointer to a member function is a function pointer that specifically points to a class member function. It's declared using _classname_::*.

>Exercise 19.16: Write a type alias that is a synonym for a pointer that can point to the `avg_price` member of `Sales_data`.

```cpp
using AvgPrice = double Sales_data::*;
```

>Exercise 19.17: Define a type alias for each distinct `Screen` member function type.

```cpp
using GetChar = char Sales_data::*;
using GetByPos = char (Sales_data::*)(pos, pos);
```

>Exercise 19.18: Write a function that uses `count_if` to count how many empty `string`s there are in a give `vector`.

```cpp
std::vector<std::string> vector;
count_if(vector.cbegin(), vector.cend(), mem_fn(&std::string::emtpy));
```
>Function template `std::mem_fn` generates wrapper objects for pointers to members, which can store, copy, and invoke a pointer to member. Both references and pointers (including smart pointers) to an object can be used when invoking a `std::mem_fn`.

>Exercise 19.19: Write a function that takes a `vector<Sales_data>` and finds the first element whose average price is greater than some given amount.

```cpp
std::vector<Sales_data>::iterator firstElement(const std::vector<Sales_data> &source)
{
    return find_if(source.cbegin(), source.cend(), [](Sales_data &item) { item.avg_price() > amount; });
}
```

Section 19.5 Nested Classes
---------------------------

>Exercise 19.20: Nest your `QueryResult` class inside `TextQuery` and rerun the programs you wrote to use `TextQuery` in § 12.3.2 (p. 490)

```cpp
    // TODO
```

Section 19.6 `union`: A Space-Saving Class
------------------------------------------

>Exercise 19.21: Write your own version of the `Token` class.
>Exercise 19.22: Add a member of type `Sales_data` to your `Token` class.
>Exercise 19.23: Aadd a move constructor and move assignment to `Token`.
>Exercise 19.24: Explain what happens if we assign a `Token` object to itself.
>Exercise 19.25: Write assignment operators that take values of each type in the `union`.

```cpp
class Token {
public:
    Token() : tok(INT), ival(0) { }
    Token(const Token &t) : tok(t.tok) { copyUnion(t); }
    Token &operator=(const Token&);
    ~Token() { if (tok == STR) sval.~string(); }
    Token &operator=(const std::string&);
    Token &operator=(char c) {
        if (tok == STR) sval.~string();
        if (tok == SD) sdval.~Sales_data();
        
        cval = c;
        tok = CHAR;
        return *this;
    }
    Token &operator=(int);
    Token &operator=(double d) {
        if (tok == STR) sval.~string();
        if (tok == SD) sdval.~Sales_data();
        
        dval = d;
        tok = DBL;
        return *this;
    }
    Token &operator=(const Sales_data &sd) {
        if (tok == SD)
            sdval = sd;
        else
            new(&sdval) Sales_data(sd);
        
        tok = SD;
        return *this;
    }
    
    Token(Token&& t) { moveUnion(t); }
    Token& operator=(Token &&other) {
        moveUnion(t);
        return *this;
    }
}
private:
    enum { INT, CHAR, DBL, STR, SD } tok;
    union {
        char cval;
        int ival;
        double dval;
        std::string sval;
        Sales_data sdval;
    };
    void copyUnion(const Token&);
    void moveUnion(Token &&token) {
        switch (token.tok) {
            case Token::INT: ival = std::move(token.ival);
            case Token::CHAR: cval = std::move(token.cval);
            case Token::DBL: dval = std::move(token.dval);
            case Token::STR: sval = std::move(token.sval);
            case Token::SD: sdval = std::move(token.sdval);
        }
    }
};
```

For 24, if `Token` supports only copy assignment, the constructor determines the value it holds and copy the value to the new object; otherwise it supports move assignment, the constructor just moves the value to the new object.
