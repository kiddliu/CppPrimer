Chapter 17
============

Exercises Section 17.1.1
------------------------
>Exercise 17.1: Define a `tuple` that holds three `int` values and initialize the members to `10`, `20`, `30`.
```
tuple<int, int, int> var(10, 20, 30);
```

>Exercise 17.2: Define a `tuple` that holds a `string`, a `vector<string>` and a `pair<string, int>`.
```
tuple<string, vector<string>, pair<string, int>> var;
```

>Exercise 17.3: Rewrite the `TextQuery` programs from § 12.3 (p. 484) to use a `tuple` instead of the `QueryResult` class. Explain which design you think is better and why.
```
// TODO:
```

Exercises Section 17.1.2
------------------------
>Exercise 17.4: Write and test your own version of the `findBook` function.
Original version on page 721.
```
typedef tuple<vector<Sales_data>::size_type,
              vector<Sales_data>::const_iterator,
              vector<Sales_data>::const_iterator> matches;

vector<matches>
findBook(const vector<vector<Sales_data>> &files, const string &book)
{
    vector<matches> result;
    
    for (auto it = files.cbegin(); it != files.cend(); ++it)
    {
        auto match = equal_range(it->cbegin(), it->cend(), book, compareIsbn);
        if (match.first != match.second)
        {
            result.push_back(make_tuple(it - files.cbegin(), match.first, match.second));
        }
    }
    
    return result;
}
```

>Exercise 17.5: Rewrite `findBook` to return a `pair` that holds an index and a `pair` of iterators.
```
typedef pair<vector<Sales_data>::size_type,
             pair<vector<Sales_data>::const_iterator, vector<Sales_data>::const_iterator>> matches;

vector<matches>
findBook(const vector<vector<Sales_data>> &files, const string &book)
{
    vector<matches> result;
    
    for (auto it = files.cbegin(); it != files.cend(); ++it)
    {
        auto match = equal_range(it->cbegin(), it->cend(), book, compareIsbn);
        if (match.first != match.second)
        {
            result.push_back(make_pair(it - files.cbegin(), make_pair(match.first, match.second)));
        }
    }
    
    return result;
}
```

>Exercise 17.6: Rewrite `findBook` so that it does not use `tuple` or `pair`.
Let's define a class 'FindBookResult' to hold the index and iterators.
```
struct FindBookResult
{
	vector<int>::size_type index;
	vector<int>::const_iterator begin;
	vector<int>::const_iterator end;

	FindBookResult(vector<int>::size_type &&index, vector<int>::const_iterator &begin, vector<int>::const_iterator &end)
		: index(index), begin(begin), end(end) { }
};

vector<FindBookResult>
findBook(const vector<vector<int>> &files, const string &book)
{
	vector<FindBookResult> result;

	for (auto it = files.cbegin(); it != files.cend(); ++it)
	{
		auto match = equal_range(it->cbegin(), it->cend(), book, compareIsbn);
		if (match.first != match.second)
		{
			result.emplace_back(it - files.cbegin(), match.first, match.second);
		}
	}

	return result;
}
```

>Exercise 17.7: Explain which version of `findBook` you prefer and why.
Of course the original version with the help of `pair`, we don't want to bother to define a data structure to represent those data.

>Exercise 17.8: What would happen if we passed `Sales_data()` as the third parameter to `accumulate` in the last code example in this section?
The object is default initialized, and addition function of 2 Sales_data objects checks if they refer to the same book. Apparently in this case it's not. Therefore it's 0.
