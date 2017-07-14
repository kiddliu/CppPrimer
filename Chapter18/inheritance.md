Multiple and Virtual Inheritance
================================

Exercises Section 18.3.1
------------------------

>Exercise 18.21: Explain the following declarations. Identify any that are in error and explain why they are incorrect:
>
>    (a) `class CADVehicle : public CAD, Vehicle { ... };`
>
>    (b) `class DblList : public List, public List { ... };`
>
>    (c) `class iostream : public istream, public ostream { ... };`

(a) The class CADVehicle inherits CAD publicly and Vehicle privately. Therefore a CADVehicle object cannot be automatically converted to Vehicle type.

(b) Error, as the DblList tries to inherit from List class twice.

(c) The class iostream inherits both istream and ostream publicly.

>Exercise 18.22: Given the following class hierarchy, in which each class defines a default constructor:
>
>       `class A { ... };`
>
>       `class B : public A { ... };`
>
>       `class C : public B { ... };`
>
>       `class X { ... };`
>
>       `class Y { ... };`
>
>       `class Z : public X, public Y { ... };`
>
>       `class MI : public C, public Z { ... };`
>
>what is the order of constructor execution for the following definition?
>
>    `MI mi;`

A -> B -> C -> X -> Y -> Z -> MI


Exercises Section 18.3.2
------------------------
>Exercise 18.23: Using the hierarchy in exercise 18.22 along with class D defined below, and assuming each class defines a default constructor, which, if any, of the following conversions are not permitted?
>
>    `class D : public X, public C { ... };
>
>    `D *pd = new D;
>
>    (a) `X *px = pd;`      (b) `A *pa = pd;`       (c) `B *pb = pd;`       (d) `C *pc = pd`;

All the conversions are permitted.

>Exercise 18.24: On page 807 we presented a series of calls made through a `Bear` pointer that pointed to a `Panda` object. Explain each call assuming we used a `ZooAnimal` pointer pointing to a `Panda` object instead.

```cpp
ZooAnimal *pAnimal = new Panda("ying_yang");
pAnimal->print(); // ok: Panda::print()
pAnimal->cuddle(); // error: not part of the ZooAnimal interface
pAnimal->highlight(); // error: not part of the ZooAnimal interface
delete pb;
```

>Exercise 18.25: Assume we have two base classes, `Base1` and `Base2`, each of which defines a virtual member named `print` and a virtual destructor. From these base classes we derive the following classes, each of which redefines the `print` function:
>
>    `class D1 : public Base1 { /* ... */ };`
>    `class D2 : public Base2 { /* ... */ };`
>    `class MI : public D1, public D2 { /* ... */ };`
>
>Using the following pointers, determine which function is used in each call:
>
>    `Base1 *pb1 = new MI;`
>
>    `Base2 *pb2 = new MI;`
>
>    `D1 *pd1 = new MI;`
>
>    `D2 *pd2 = new MI;`
>
>    (a) `pb1->print();` (b) `pd1->print();` (c) `pd2->print();`
>
>    (d) `delete pb2;` (e) `delete pd1;` (f) `delete pd2;`

(a) (b) (c) all calls print() of MI. (d) (e) (f) all deletes MI in a reversed order: MI -> D2 -> Base2 -> D1 -> Base1.


Exercises Section 18.3.3
------------------------
>Exercise 18.26: Given the hierarchy in the box on page 810, why is the following call to `print` an error? Revise `MI` to allow this call to `print` to compile and execute correctly.
>
>    MI mi;
>
>    mi.print(42);

The intention is to call `void print(int) const;` but `as always, name lookup happens before type checking`. Then the compiler finds `void print(std::vector<double>);` only. To fix this, calls `mi.Base1::print(42);` instead.

>Exercise 18.27: Given the class hierarchy in the box on page 810 and assuming we add a function named `foo` to MI as follows:
>
>```
>    int ival;
>    double dval;
>    void MI::foo(double cval)
>    {
>        int dval;
>        // exercise questions occur here
>    }
>
>(a) List all the names visible from within MI::foo.
>
>(b) Are any names visible from more than one base class?
>
>(c) Assign to the local instance of `dval` the sum of the `dval` member of `Base1` and the `dval` member of `Derived`.
>
>(d) Assign the value of the last element in `MI::dvec` to `Base2::fval`.
>
>(e) Assign `cval` from `Basel` to the first character in `sval` from `Derived`.

(a) local variable: `int dval`, protected `MI` class member variables: `int *val`, `std::vector<double> dvec`, protected `Derived` class member variables: 'std::string sval', `double dval`, protected `Base1` class member variables: `int ival`, `double dval`, `char cval`, protected `Base2` class member variables: `double fval`

(b) Yes, local variable `int dval` hides `double dval`, protected `MI` class member `int *val` hides protected `Base1` class member `int ival`.

(c) `int dval = static_cast<int>(Base1::dval + Derived::dval);`

(d) `if (dvec.size() != 0) Base::fval = dvec.back();`

(e) `if (sval.size() != 0) Base1::cval = sval.front();`
