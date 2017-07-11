The `bitset` Type
=================

Exercises Section 17.2.1
------------------------
>Exercise 17.9: Explain the bit pattern each of the following `bitset` objects contains:
>
>  (a) bitset<64> bitvect(32);
>
>  (b) bitset<32> bv(1010101); 
>
>  (c) string bstr; cin >> bstr; bitset<8>bv(bstr);

(a) 0000000000000000000000000000000000000000000000000000000000010000

(b) 00000000000011110110100110110101

(c) It depends on the input

Exercises Section 17.2.2
------------------------
>Exercise 17.10: Using the sequence 1, 2, 3, 5, 8, 13, 21, initilalize a `bitset` that has a 1 bit in each position corresponding to a number in this sequence. Default initialize another `bitset` and write a small program to turn on each of the appropriate bits.

```cpp
vector<int> indexes{ 1, 2, 3, 5, 8, 13, 21 };
unsigned long initializer = 0;
for (auto &index : indexes)
{
    initializer |= 1UL << index;
}
bitset<32> value(initializer);

bitset<32> anotherValue;
for (auto &index : indexes)
{
    anotherValue.set(index);
}

```

>Exercise 17.11: Define a data structure that contains an integral object to track responses to a true/false quiz containing 10 questions. What changes, if any, would you need to make in your data structure if the quiz had 100 questions?

```cpp
#ifndef QUESTION_RESPONSE_H
#define QUESTION_RESPONSE_H

template <size_t N>
class QuestionResponse
{
public:
    QuestionResponse() = default;
private:
    bitset<N> response;
};

#endif // QUESTION_RESPONSE_H
```

>Exercise 17.12: Using the data structure from the previous question, write a function that takes a question number and a value to indicate a true/false answer and updates the quiz results accordingly.

```cpp
void updateResponse(const size_t &number, bool answer)
{
    response.set(number, answer)
}
```

>Exercise 17.13: Write an integral object that contains the correct answers for the true/false quiz. Use it to generate grades on the quiz for the data structure from the previous two exercises.

```cpp
template <size_t M>
size_t grade(const QuestionResponse<M> &correct, const QuestionResponse<M> &answer)
{
    auto result = correct ^ answer;
    result.flip();
    return result.count();
}
```
