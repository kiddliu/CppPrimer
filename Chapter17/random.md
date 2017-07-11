Random Numbers
==============

Exercises Section 17.4.1
------------------------
>Exercise 17.28: Write a function that generates and returns a uniformly distributed random `unsigned int` each time it is called.

>Exercise 17.29: Allow the user to supply a seed as an optional argument to the function you wrote in the previous exercise.

>Exercise 17.30: Revise your function again this time to take a minimum and maximum value for numbers that the function should return.
```cpp
unsigned getRandomUnsignedInt(unsigned seed, unsigned min = 0, unsigned max = numeric_limits<unsigned>::max())
{
    static default_random_engine engine(seed);
    static uniform_int_distribution<unsigned> uniformIntDistribution(min(first, second), max(first, second));

    return uniformIntDistribution(engine);
}
```

Exercises Section 17.4.2
------------------------
>Exercise 17.31: What would happend if we defined `b` and `e` inside the `do` loopp of the game-playing program from this section?

Quote from page 782:
>Because engines return the same sequence of numbers, it is essential that we declare engines outside of loops. Otherwise, we'd create a new engine on each iteration and generate the smae values on each iteration. Similarly, distributions may retain state and should also be deined outside loops.

>Exercise 17.32: What would happen if we defined `resp` inside the loop?

Then `resp` is in the scope of the loop, and the condition specified in `while` fails to compile.

>Exercise 17.33: Write a version of the word transformation program from § 11.3.6 (p. 440) that allows multiple transformations for a given word and randomly selects which transformation to apply.

```cpp
map<string, vector<string>> buildMap(istream &map_file)
{
    map<string, vector<string>> trans_map;
    string key, value;
    while (map_file >> key && getline(map_file, value))
    {
        if (value.size() > 1) {
            string substr(value.substr(1));
            if (trans_map.find(key) == trans_map.end()) {
                trans_map[key] = vector<string>{substr};
            } else {
                trans_map[key].emplace_back(substr);
            }
        }
        else throw runtime_error("no rule for " + key);
    }
    return trans_map;
};

string randomIndex(vector<string> transformations) {
    static default_random_engine engine;
    static uniform_int_distribution<unsigned> uniformIntDistribution(0, transformations.size() - 1);

    return transformations.at(uniformIntDistribution(engine));
}

const string &
transform(const string &s, const map<string, vector<string>> &m)
{
    auto map_it = m.find(s);
    if (map_it != m.cend()) return map_it->second;
    else return s;
}

void word_transform(istream &map_file, istream &input)
{
    auto trans_map = buildMap(map_file);
    string text;
    while (getline(input, text))
    {
        istringstream stream(text);
        string word;
        bool firstword = true;
        while (stream >> word) {
            if (firstword) firstword = false;
            else cout << " ";

            cout << transform(word, trans_map);
        }
        cout << endl;
    }
}
```
