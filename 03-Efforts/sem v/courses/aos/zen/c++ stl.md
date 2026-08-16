___
**C++ Skeleton & Basics**
___
The standard template library (STL) provides pre-written algorithms and containers. To avoid including individual libraries (`<math.h>`, `<string.h>`), use the master header. The `using namespace std;` statement prevents having to type `std::` before standard functions like `cin` or `cout`. Void functions execute without returning a value, while return-type functions yield a specific data type.

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    // code
    return 0;
}

```

---
**Pairs**
A part of the utility library used to store two linked values of any data type. Pairs can be nested to store three or more variables. Access elements using `.first` and `.second`. You can also create arrays of pairs.
* `pair<int, int> p = {1, 3};`
* `cout << p.first << " " << p.second;`
* `pair<int, pair<int, int>> p2 = {1, {3, 4}};`
* `cout << p2.first << " " << p2.second.second;`
* `pair<int, int> arr[] = { {1, 2}, {2, 5}, {5, 1} };`
* `cout << arr[1].second;` // Prints 5

---
**Vectors
A dynamic array container that automatically resizes. When you don't know the required size beforehand, always use a vector.
* `vector<int> v;` creates an empty container.
* `v.push_back(1);` appends 1 to the end.
* `v.emplace_back(2);` acts identically to push_back but is dynamically faster and auto-assumes data types for pairs (e.g., `v.emplace_back(1, 2)` instead of `v.push_back({1, 2})`).
* `vector<int> v(5, 100);` creates a vector of size 5 with all elements as 100.
* `vector<int> v(5);` creates a vector of size 5 initialized to 0 (or garbage value depending on compiler).
* `vector<int> v2(v);` copies elements from v into v2.

Access elements exactly like standard arrays using `v[0]`, `v[1]`, or `v.back()` to access the last element. To iterate over a vector, iterators act as memory pointers.
* `vector<int>::iterator it = v.begin();` points to the memory address of the first element. Dereference with `*it` to get the value.
* `v.end()` points to the memory address strictly **after** the last element. You must decrement it (`it--`) to access the final element.
* `v.rend()` and `v.rbegin()` reverse the standard iterator flow (rarely used).
* Instead of typing long iterator declarations, use `auto`, which auto-assigns the correct data type.
* Common shortcut for iteration: `for(auto it : v) { cout << it << " "; }` iterates directly on the elements, not the memory addresses.

Deletion and Insertion use iterators. Note that inserting into a vector is costly ($O(N)$) because elements must shift.
* `v.erase(v.begin() + 1);` deletes the second element.
* `v.erase(v.begin() + 1, v.begin() + 4);` deletes a range. The end address is always **exclusive** $[start, end)$.
* `v.insert(v.begin(), 300);` inserts 300 at the start.
* `v.insert(v.begin() + 1, 2, 10);` inserts two instances of 10 at the second position.
* `v.size()` returns element count, `v.pop_back()` removes the last element, `v.clear()` empties the vector, and `v.empty()` returns true if size is 0.

---
**List & Deque**
`list` maintains an internal doubly linked list. It provides everything a vector does, but also allows $O(1)$ front operations which are highly expensive in vectors.
* `list<int> ls;`
* `ls.push_front(5);` and `ls.emplace_front(5);` insert at the beginning cheaply.
`deque` behaves almost exactly like a list and vector, supporting `push_front` and `push_back`.

---
**Stack
A Last-In-First-Out (**LIFO**) data structure. You cannot access elements by index. All main operations occur in **$O(1)$** time.
* `stack<int> st;`
* `st.push(1); st.push(2); st.emplace(3);`
* `st.top();` returns 3 (the last inserted element).
* `st.pop();` removes the top element (3).
---
**Queue**
A First-In-First-Out (**FIFO**) data structure. Imagine a ticket counter. All operations are **$O(1)$** time.
* `queue<int> q;`
* `q.push(1); q.push(2); q.emplace(4);`
* `q.back() += 5;` targets the last element (4 becomes 9).
* `q.front();` accesses the first element (1).
* `q.pop();` removes the first element (1).

---
**Priority Queue**
Internally maintains a tree structure to keep the largest element at the top (Max Heap). Storing elements is not linear.
* `priority_queue<int> pq;`
* `pq.push(5); pq.push(8); pq.push(2);`
* `pq.top();` returns 8.
* To create a Min Heap (smallest element at top): `priority_queue<int, vector<int>, greater<int>> pq;`
* Time Complexity: `push` is **$O(\log N)$**, `pop` is **$O(\log N)$**, `top` is **$O(1)$**.

---
**Set**
A tree-based container that stores elements in **sorted** order and guarantees they are **unique**. All operations occur in **$O(\log N)$** time.
* `set<int> st;`
* `st.insert(1); st.emplace(2);`
* `st.find(3);` returns an iterator pointing to 3. If 3 is not in the set, it returns `st.end()`.
* `st.count(1);` returns 1 if present, or 0 if absent (since elements are unique).
* `st.erase(5);` deletes 5 and maintains sorted order.
* `st.erase(it);` deletes the value at the specific iterator in $O(1)$ time. Range erasure `st.erase(it1, it2)` works similar to vectors.
Note (not in video): The video mentions `lower_bound()` and `upper_bound()` exist in sets. `st.lower_bound(x)` returns an iterator to the first element $\ge x$. `st.upper_bound(x)` returns an iterator to the first element $> x$.

---
**Multiset & Unordered Set**
`multiset` stores elements in sorted order but **allows duplicates**.
* `ms.count(1);` returns the total occurrences of 1.
* `ms.erase(1);` deletes **all** occurrences of 1.
* `ms.erase(ms.find(1));` deletes only the **first** occurrence of 1 because `find()` returns an iterator to just one instance.
`unordered_set` stores unique elements but in a **randomized** order.
* It lacks `lower_bound` and `upper_bound`.
* Most operations execute in **$O(1)$** average time complexity.
* Worst-case complexity is **$O(N)$** (happens very rarely due to internal hash collisions).

---
**Map**
Stores data in key-value pairs where keys are **unique** and **sorted**. Any data type can act as a key or a value (e.g., `map<pair<int,int>, int>`). Operates in **$O(\log N)$** time.
* `map<int, int> mpp;`
* `mpp[1] = 2;` maps key 1 to value 2.
* `mpp.insert({3, 1});`
* If a key does not exist, accessing it (like `mpp[5]`) will assign and return 0 (or null).
* `for(auto it : mpp) { cout << it.first << " " << it.second; }` iterates in sorted order of keys.
* `mpp.find(3);` returns an iterator to the key-value pair. Access value via `it->second`. Returns `mpp.end()` if not found.
`multimap` allows duplicate keys but maintains sorted order. `unordered_map` retains unique keys but randomizes order, offering **$O(1)$** average time and **$O(N)$** worst-case time.

---
**Algorithms & Custom Comparators**
The STL includes universal functions that replace manual sorting loops. Iterators must be provided for range bounds `[start, end)`.
* `sort(a, a + n);` sorts a standard array of size $n$.
* `sort(v.begin(), v.end());` sorts a vector in ascending order.
* `sort(v.begin(), v.end(), greater<int>());` uses a built-in comparator to sort descending.
When standard sorting isn't enough, build a custom comparator. A comparator is a boolean function that defines the "correct order" for two elements $p1$ and $p2$.
* Rule of thumb: If $p1$ should logically appear before $p2$, return `true`. Otherwise, return `false` to force an internal swap.
* Example constraint: Sort an array of pairs primarily by the second element ascending. If second elements match, sort by the first element descending.

```cpp
bool comp(pair<int,int> p1, pair<int,int> p2) {
    if (p1.second < p2.second) return true; // Correct order
    if (p1.second > p2.second) return false; // Needs swap
    // They are equal, execute tie-breaker logic
    if (p1.first > p2.first) return true; // Correct order
    return false; // Needs swap
}
// Usage: sort(a, a+n, comp);

```

Bit manipulation and math operations:
* `__builtin_popcount(7);` returns the number of set bits (1s) in binary. 7 is `111`, so it returns 3.
* `__builtin_popcountll(x);` must be used for `long long` variables to avoid overflow errors.
* `next_permutation(s.begin(), s.end())` modifies a string/array to its next dictionary/lexicographical arrangement. It returns false when no more permutations exist.
* Crucial Pitfall: To generate **all** permutations, the initial structure must be fully sorted first.
* `max_element(v.begin(), v.end())` returns an iterator pointing to the maximum value. Dereference with `*` to get the raw number.
Note (not in video): When doing recursive DSA (like generating permutations manually), always pass containers by reference (`vector<int>& v`) to avoid expensive $O(N)$ copies on every function call. Be mindful of limits using `INT_MAX` and `INT_MIN` for comparisons.


___
zen
___
**Missing STL Concepts & Mechanics**

* **Templates**: `template <class T>` creates type-independent logic. Compiler figures out the exact type automatically at compile-time.
* **Iterator Types**: **Random Access** (`vector`, `deque`) lets you do fast pointer jumps (`it + 5`) in $O(1)$. **Bidirectional** (`list`, `set`, `map`) only allows stepping one at a time (`it++`, `it--`).
* **Iterator Traps**: Resizing a `vector` breaks all active iterators. Erasing from a `set`/`map` only invalidates the specific iterator you deleted.
* **std::distance**: `distance(it1, it2)` calculates gaps. Takes $O(1)$ for vectors/deques but takes $O(N)$ for sets/maps.
**Vector & String Details**
* **Capacity vs Size**: `size()` is how many elements exist. `capacity()` is total allocated memory. Vectors double capacity when full, making `push_back` an **amortized** $O(1)$ operation.
* **std::getline**: `getline(cin, s)` reads whole lines with spaces. Standard `cin >> s` fails on spaces.
* **std::string::substr**: `s.substr(index, length)` cuts out part of a string.
* **std::string::c_str**: `s.c_str()` converts to a raw C-style string for legacy operations.
**Algorithm Additions**
* **std::nth_element**: Uses QuickSelect to put the nth element exactly where it belongs if sorted. Left side gets smaller values, right side gets bigger ones. **Average-Case**: $O(N)$, **Worst-Case**: $O(N \log N)$.
* **std::unique**: Compresses duplicate consecutive elements and returns an iterator to the new end. You must call `sort()` first if you want purely distinct elements. Runs in $O(N)$.
* **std::reverse**: Flips elements in a range in $O(N)$.
* **std::binary_search**: Just returns `true`/`false` for existence in $O(\log N)$.
**Container Pitfalls**
* **List Sort Fail**: Regular `std::sort` crashes on `std::list` since lists have no random access. Always use `list.sort()` for $O(N \log N)$.
* **Set/Map Binary Search Fail**: Generic `std::lower_bound(s.begin(), s.end(), val)` forces $O(N)$ time on sets/maps. Always use `s.lower_bound(val)` to keep it $O(\log N)$.
* **Unordered Internals**: Unordered sets/maps use Hash Tables with separate chaining. When the load factor hits 1.0, capacity doubles and elements rehash.
* **Pair Hash Fail**: C++ has no default hash for `std::pair`. Trying to declare `unordered_map<pair<int, int>, int>` throws a compiler error without a custom hash struct.
**Custom Logic & Code**
* **Strict Weak Ordering**: Custom sort functions **must** return `false` for equal elements. Returning `true` causes logical loops and runtime crashes.
* **Sort Lambdas**: Use inline lambdas for custom sorts instead of external functions:

```cpp
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;
});

```

* **Custom Hash Structure**: Needed to bypass the pair hashing fail in unordered containers:

```cpp
struct myHash {
    size_t operator()(pair<int, int> p) const {
        return p.first ^ p.second;
    }
};
unordered_set<pair<int, int>, myHash> mySet;

```