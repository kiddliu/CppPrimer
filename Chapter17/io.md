The IO Library Revisited
========================

Exercises Section 17.5.1
------------------------

>Exercise 17.34: Write a program that illustrates the use of each manipulator in Tables 17.17 (p. 757)
```cpp
cout << boolalpha << true << noboolalpha << endl;
cout << showbase;
cout << "oct " << oct << 20 << " " << 1024 << endl;
cout << "hex " << hex << 20 << " " << 1024 << endl;
cout << "dec " << dec << 20 << " " << 1024 << endl;
cout << noshowbase;
cout << showpoint << 10.0 << noshowpoint << endl;
cout << showpos << 32767 << noshowpos << endl;
cout << uppercase << showbase << hex << 20 << " " << 1024 << dec << noshowcase << nouppercase << endl;
cout << left << "i: " << setw(12) << -16 << "next col" << right << endl;
cout << internal << "i: " << setw(12) << -16 << "next col" << endl;

double d(100 * sqrt(2.0));
cout << scientific << d << endl;
cout << fixed << d << endl;
cout << hexfloat << d << endl;
cout << defaultfloat << d << endl;

cout << unitbuf << 1 << 2 << 3 << 4 << 5 << nounitbuf << endl;

char c1, c2, c3;
std::istringstream("a b c") >> c1 >> c2 >> c3;
std::cout << "Default  behavior: c1 = " << c1 << " c2 = " << c2 << " c3 = " << c3 << '\n';
std::istringstream("a b c") >> std::noskipws >> c1 >> c2 >> c3;
std::cout << "noskipws behavior: c1 = " << c1 << " c2 = " << c2 << " c3 = " << c3 << '\n';
```

>Exercise 17.35: Write a version of the program from page 758, that printed the square root of 2 but this time print hexadecimal digits in uppercase.
```cpp
cout << hexfloat << uppercase << sqrt(2.0) << nouppercase << defaultfloat << endl;
```

>Exercise 17.36: Modify the program from the previous exercise to print the various floating-point values so that they line up in a column.
```cpp
double d(100 * sqrt(2.0));
cout << setw(16) << "scientific: " << scientific << setw(16) << d << endl;
cout << setw(16) << "fixed: " << fixed << setw(16) << d << endl;
cout << setw(16) << "hexfloat: " << hexfloat << setw(16) << d << endl;
cout << setw(16) << "defaultfloat: " << defaultfloat << setw(16) << d << endl;
```

Exercises Section 17.5.3
------------------------
>Exercise 17.39: Write your own version of the seek program presented in this section.
```cpp
#include <iostream>
#include <fstream>
#include <vector>

using namespace std;

int main() {
    fstream inOut("copyOut", fstream::ate | fstream::in | fstream:: out);
    if (!inOut) return EXIT_FAILURE;
    
    auto end = inOut.tellg();
    inOut.seekg(0, fstream::beg);
    size_t length(0);
    string line;
    vector<size_t> result;
    
    while (inOut && inOut.tellg() != end
            && getline(inOut, line)) {
        length += line.size() + 1;
        result.push_back(length);
    }
    
    inOut.seekp(0, fstream::end);
    for_each(result.cbegin(), result.cend(), [](const size_t &s){
        inOut << s << " ";
    });
    inOut << "\n";
    
    return 0;
}
```
