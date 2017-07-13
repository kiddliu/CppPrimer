Namespaces
==========
Exercises Section 18.2.1
------------------------

>Exercise 18.12: Organize the programs you have written to answer the questions in each chapter into their own namespaces. That is, namespace `chapter15` would contain code for the `Query` programs and `chapter10` would contain the `TextQuery` code. Using this structure, compile the `Query` code example.

```cpp
// TODO:
```
>Exercise 18.13: When might you use an unnamed namespace?

Quote from [this](https://stackoverflow.com/a/357427/1937578)

>Having something in an anonymous namespace means it's local to this [translation unit](https://en.wikipedia.org/wiki/Translation_unit_(programming)) (.cpp file and all its includes) this means that if another symbol with the same name is defined elsewhere there will not be a violation of the [One Definition Rule](http://en.wikipedia.org/wiki/One_Definition_Rule) (ODR).
>
>This is the same as the C way of having a static global variable or static function but it can be used for class definitions as well (and should be used rather than static in C++).
>
>All anonymous namespaces in the same file are treated as the same namespace and all anonymous namespaces in different files are distinct. An anonymous namespace is the equivalent of:
>```
>namespace __unique_compiler_generated_identifer0x42 {
>     ...
>}
>using namespace __unique_compiler_generated_identifer0x42;
>```

>Exercise 18.14: Suppose we have the following declaration of the operator* that is a member of the nested namespace `mathLib::MatrixLib`:
>```cpp
>    namespace mathLib {
>        namespace MatrixLib {
>            class matrix { /* ... */ };
>            matrix operator*(const matrix &, const matrix &);
>            // ...
>        }
>    }
>```
>How would you declare this operator in global scope?
```cpp
mathLib::MatrixLib::matrix 
mathLib::MatrixLib::operator*(const matrix &, const matrix &);
```

Exercises Section 18.2.2
------------------------
>Exercise 18.15: Explain the differences between `using` declarations and directives.

Quote:

>A **using declaration** introduces only one namespace member at a time. It allows us to be very specific regarding which names are used in our programs.
>
>A **using directive**, like a `using` declaration, allows us to use the unqualified form of a namespace name. Unlike a `using` declaration, we retain no control over which names are made visible...they all are.

>Exercise 18.16: Explain the following code assuming `using` declarations for all the members of namespace `Exercise` are located at the location labeled *position 1*. What if the they appear at *position 2* instead? Now answer the same question but replace the `using` declearations with a `using` directive for namespace Exercise.
>```cpp
>    namespace Exercise {
>        int ivar = 0;
>        double dvar = 0;
>        const int limit = 1000;
>    }
>    int ivar = 0;
>    // position 1
>    void manip() {
>        // position 2
>        double dvar = 3.1416;
>        int iobj = limit + 1;
>        ++ivar;
>        ++::ivar;
>```

>Exercise 18.17: Write code to test your answers to the previous question.

With `using` declarations of all the members of namespace `Exercise` at position 1, the declaration of `ivar` is already in scope then it's an error. If the declarations go to position 2, the declaration of `dvar` is already in scope then it's an error.

By switching from using declaration to using directive at position 1, `ivar` is ambiguous; if the directive goes to position 2, it's still the same problem.
