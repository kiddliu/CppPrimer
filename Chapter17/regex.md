Regular Expressions
===================

Exercises Section 17.3.1
------------------------

>Exercise 17.14: Write several regular expressions designed to trigger various errors. Run your program to see what output your compiler generateds for each error.

```cpp
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

```cpp
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

```cpp
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

```cpp
string s;
getline(cin, s);

regex re("[[:alpha:]]*[^c]ei[[:alpha:]]*", regex::icase);
for (sregex_iterator it(s.cbegin(), s.cend(), re), end; it != end; ++it)
{
    cout << it->str() << endl;
}
```

Exercises Section 17.3.3
------------------------

>Exercise 17.19: Why is it okay to call `m[4].str()` without first checking whether `m[4]` was matched?

`bool valid(const smatch &m)` is only called when there is a match. In consideration that `m[4]` and `m[6]` is checking the delimeter, there are 2 circumstances: a) a candidate character is found, or b) no character there. For both cases we could use string comparison to check if they're the same.

>Exercise 17.20: Write your own version of the program to validate phone numbers.

The original version on page 740 is OK.

>Exercise 17.21: Rewrite your phone number program from § 8.3.2 (p. 323) to use the `valid` function defined in this section.

```cpp
bool valid(const string &num)
{
    regex re("(\\()?(\\d{3})(\\))?([-. ])?(\\d{3})([-. ])?(\\d{4})");
    smatch match;
    
    return regex_match(num, match, re) && valid(match);
}
```

>Exercise 17.22: Rewrite your phone program so that it allows any number of whitespace characters to seperate the three parts of a phone number.

```cpp
regex re("(\\()?(\\d{3})(\\))?(\\s*)(\\d{3})(\\s*)(\\d{4})")
```

>Exercise 17.23: Write a regular express to find zip codes. A zip code can have five or nine digits. The first five digits can be separated from the remaining four by a dash.


```cpp
regex re("^\\d{5}(-\\d{4})?$")
```

Exercises Section 17.3.4
------------------------

>Exercise 17.24: Write your own version of the program to reformat phone numbers.

>Exercise 17.25: Rewrite your phone program so that it writes only the first phone number for each person.

```cpp
string s;
s.append("morgan (201) 555-2368 862-555-0123\n");
s.append("drew (973)555.0130\n");
s.append("lee (609) 555-0132 2015550175 800.555-0000\n");
istringstream stream(s);

regex re("(\\()?(\\d{3})(\\))?([-. ])?(\\d{3})([-. ])?(\\d{4})");
string format("$2.$5.$7");

string line;
while (getline(stream, line))
{
    cout << regex_replace(line, re, format, format_first_only | format_no_copy) << endl;
}
```

>Exercise 17.26: Rewrite your phone program so that it writes only the second and subsequent phone numbers for people with more than one phone number.

```cpp
string s;
s.append("morgan (201) 555-2368 862-555-0123\n");
s.append("drew (973)555.0130\n");
s.append("lee (609) 555-0132 2015550175 800.555-0000\n");
istringstream stream(s);

regex re("(\\()?(\\d{3})(\\))?([-. ])?(\\d{3})([-. ])?(\\d{4})");
string format("$2.$5.$7 ");

string line;

while (getline(stream, line))
{
    string result(regex_replace(line, re, format, format_no_copy));

    if (result.size() > 13) cout << result.substr(13) << endl; // if there's more than 1 number let's output it
}
```

>Exercise 17.27: Write a program that reformats a nine-digit zip code as ddddd-dddd.

```cpp
regex re("(\\d{5})([-. ])?(\\d{4})");
string format("$1-$3");

string line;

while (getline(cin, line))
{
    cout << regex_replace(line, re, format, format_no_copy)) << endl;
}
```
