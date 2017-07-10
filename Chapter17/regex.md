Regular Expressions
===================

Exercises Section 17.3.1
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

>Exercise 17.15: Write a program using the pattern that finds words that violate the "_i_ before _e_ except after _c_" rule. Have your program prompt the user to supply a word and indicate whether the word is okay or not. Test your program with words that do and do not violate the rule.

```
string word;
regex re("[[:alpha:]]*([^c]ie|cei)[[:alpha:]]*");

while (true)
{
    cout << "Please provide a word, type 'exit' to exit: ";
    cin >> word;
    if (word == "exit") break;

    cout << "Word " << word << (regex_match(word, re) ? " is OK" : " is not OK") << endl;
}
```

>Exercise 17.16: What would happen if your regex object in the previous program were initialized with "[^c]ei"? Test your program using that pattern to see whether your expectations were correct.

```
string word;
regex re("[^c]ei");

while (true)
{
    cout << "Please provide a word, type 'exit' to exit: ";
    cin >> word;
    if (word == "exit") break;

    cout << "Word " << word << (regex_match(word, re) ? " is OK" : " is not OK") << endl;
}
```
So the result is just the opposite to the correct answer.
