Regular Expressions
===================

Exercises Section 17.3.1
>Exercise 17.15: Write a program using the pattern that finds words that violate the "_i_ before _e_ except after _c_" rule
------------------------

>Exercise 17.14: Write several regular expressions designed to trigger various errors. Run your program to see what output your compiler generateds for each error.

```
try 
{
    regex re;
    re.assign("\\");    // _S_error_escape
    re.assign(")");     // _S_error_paren
    re.assign("[a-9]"); // _S_error_range
} 
catch (const regex_error &error)
{
    cout << error.what() << " " << error.code() << endl;
}
```
