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

