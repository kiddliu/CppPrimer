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
regex re("[[:alpha:]]*([^c]ei)[[:alpha:]]*");

while (true)
{
    cout << "Please provide a word, type 'exit' to exit: ";
    cin >> word;
    if (word == "exit") break;

    cout << "Word " << word << (regex_match(word, re) ? " violates the spelling rule." : " is fine") << endl;
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
`regex_match` tries to match the whole string, and by removing the `[[:alpha:]]*` at the start and the end of the pattern, it tries to match the exact mispelling part. So `regex_search` is the much appropriate function to call with. 

Exercises Section 17.3.1
------------------------

>Exercise 17.17: Update your program so that it finds all the words in an input sequence that violate the "ei" grammar rule.

>Exercise 17.18: Revise your program to ignore words that contain "ei" but are not misspellings, such as "albeit" and "neighbor".

```
string s;
getline(cin, s);

regex re("[[:alpha:]]*[^c]ei[[:alpha:]]*", regex::icase);
for (sregex_iterator it(s.cbegin(), s.cend(), re), end; it != end; ++it)
{
    cout << it->str() << endl;
}
```
