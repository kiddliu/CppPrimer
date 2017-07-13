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

Exercises Section 18.2.3
------------------------
>Exercise 18.18: Given the following typical definition of swap § 13.3 (p. 517), determine which version of swap is used if `mem1` is a `string`. What if `mem1` is an `int`? Explain how name lookup works in both cases.
>```cpp
>    void swap(T v1, T v2)
>    {
>        using std::swap;
>        swap(v1.mem1, v2.mem1);
>        // swap remaining members of type T
>    }
>```
The compiler searches for the qualified name in reverse order, then it checks the `using` declaration and finds the template function `swap`. If `mem1` is of type `string`, the compiler finds the best overloaded specialization
```cpp
template< class CharT, class Traits, class Alloc >
void swap( std::basic_string<CharT, Traits, Alloc> &lhs,
           std::basic_string<CharT, Traits, Alloc> &rhs );
```
; if `int` is the case, it finds no specialization at all and loads the default implementation which utilizes `std::move(__val)`
```cpp
  /**
   *  @brief Swaps two values.
   *  @param  __a  A thing of arbitrary type.
   *  @param  __b  Another thing of arbitrary type.
   *  @return   Nothing.
  */
  template<typename _Tp>
    inline void
    swap(_Tp& __a, _Tp& __b)
#if __cplusplus >= 201103L
    noexcept(__and_<is_nothrow_move_constructible<_Tp>,
	            is_nothrow_move_assignable<_Tp>>::value)
#endif
    {
      // concept requirements
      __glibcxx_function_requires(_SGIAssignableConcept<_Tp>)

      _Tp __tmp = _GLIBCXX_MOVE(__a);
      __a = _GLIBCXX_MOVE(__b);
      __b = _GLIBCXX_MOVE(__tmp);
    }
```

>Exercise 18.19: What if the call to `swap` was `std::swap(v1.mem1, v2.mem1)`?

The compiler skips the name searching steps and finds the best overloaded function directly to compile.
