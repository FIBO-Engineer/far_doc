# C++ Iterators

Iterators are a core concept in C++ that provide a uniform way to traverse through various container types (like arrays, vectors, lists, maps, etc.) without needing to know the underlying implementation details.

## Basic Iterator Concepts

Iterators act as a bridge between algorithms and containers:

```cpp
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    
    // Iterator declaration
    std::vector<int>::iterator it;
    
    // Traversing using iterators
    for (it = numbers.begin(); it != numbers.end(); ++it) {
        std::cout << *it << " ";  // Dereferencing to access the value
    }
    // Output: 1 2 3 4 5
    
    // Alternative with auto (C++11)
    for (auto it = numbers.begin(); it != numbers.end(); ++it) {
        std::cout << *it << " ";  
    }
}
```

## Iterator Hierarchy

C++ provides several categories of iterators with increasing capabilities:

### 1. Input Iterators

Input iterators can read elements from a sequence, moving only forward one step at a time and supporting only a single pass through the data.

```cpp
#include <iostream>
#include <sstream>
#include <iterator>

int main() {
    // std::istream_iterator is an input iterator
    std::istringstream input("1 2 3 4 5");
    std::istream_iterator<int> it(input);  // Points to first int
    std::istream_iterator<int> end;        // End-of-stream iterator
    
    // Read from input stream one element at a time
    while (it != end) {
        std::cout << "Read: " << *it << std::endl;
        ++it;  // Move to next element (can't go back)
    }
    
    // Can't reset or reuse the iterator - single pass only
}
```

### 2. Output Iterators

Output iterators can write elements to a destination sequence, moving only forward and supporting only a single pass.

```cpp
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> data = {1, 2, 3, 4, 5};
    
    // std::ostream_iterator is an output iterator
    std::ostream_iterator<int> output_it(std::cout, " ");
    
    // Copy elements to output stream
    std::copy(data.begin(), data.end(), output_it);
    // Output: 1 2 3 4 5
    
    // Using with algorithms
    std::vector<int> squares;
    std::back_insert_iterator<std::vector<int>> back_it(squares);
    // Or simply: auto back_it = std::back_inserter(squares);
    
    // Transform and insert at back
    std::transform(data.begin(), data.end(), back_it,
                  [](int x) { return x * x; });
    
    // View results
    for (int num : squares) {
        std::cout << num << " ";
    }
    // Output: 1 4 9 16 25
}
```

### 3. Forward Iterators

Forward iterators can both read and write elements, moving only forward but supporting multiple passes.

```cpp
#include <forward_list>  // Singly-linked list
#include <iostream>
#include <algorithm>

int main() {
    // std::forward_list provides forward iterators
    std::forward_list<int> flist = {1, 2, 3, 4, 5};
    
    // Forward traversal
    std::forward_list<int>::iterator it = flist.begin();
    std::cout << *it << std::endl;  // 1
    ++it;
    std::cout << *it << std::endl;  // 2
    
    // Can't go backwards: --it; // Error!
    
    // But can make multiple passes
    for (auto it1 = flist.begin(); it1 != flist.end(); ++it1) {
        std::cout << "First pass: " << *it1 << std::endl;
    }
    
    for (auto it2 = flist.begin(); it2 != flist.end(); ++it2) {
        std::cout << "Second pass: " << *it2 << std::endl;
    }
    
    // Can modify values in-place
    for (auto it = flist.begin(); it != flist.end(); ++it) {
        *it = *it * 2;
    }
    
    // View modified list
    for (int val : flist) {
        std::cout << val << " ";  // 2 4 6 8 10
    }
}
```

### 4. Bidirectional Iterators

Bidirectional iterators can move both forward and backward, reading and writing elements with multiple passes.

```cpp
#include <list>  // Doubly-linked list
#include <iostream>

int main() {
    // std::list provides bidirectional iterators
    std::list<int> dlist = {10, 20, 30, 40, 50};
    
    // Bidirectional traversal
    std::list<int>::iterator it = dlist.begin();
    ++it;  // Forward to 20
    ++it;  // Forward to 30
    std::cout << *it << std::endl;  // 30
    
    --it;  // Backward to 20
    std::cout << *it << std::endl;  // 20
    
    // Traverse backward through entire list
    auto rit = dlist.end();
    while (rit != dlist.begin()) {
        --rit;  // Have to predecrement since end() is past-the-end
        std::cout << *rit << " ";
    }
    // Output: 50 40 30 20 10
}
```

### 5. Random Access Iterators

Random access iterators provide all iterator operations plus direct access to any element with constant time complexity.

```cpp
#include <vector>
#include <iostream>

int main() {
    // std::vector provides random access iterators
    std::vector<int> vec = {10, 20, 30, 40, 50};
    
    // Random access operations
    std::vector<int>::iterator it = vec.begin();
    
    // Direct offset access
    std::cout << it[2] << std::endl;     // 30 (like array access)
    std::cout << *(it + 3) << std::endl; // 40 (pointer arithmetic)
    
    // Jump directly to an element
    it += 4;  // Jump to the 5th element
    std::cout << *it << std::endl;  // 50
    
    // Jump backward
    it -= 2;  // Go back to the 3rd element
    std::cout << *it << std::endl;  // 30
    
    // Iterator arithmetic
    auto it2 = vec.begin();
    std::cout << "Distance: " << it - it2 << std::endl;  // 2
    
    // Comparison operators
    std::cout << "it > it2: " << (it > it2) << std::endl;  // 1 (true)
    std::cout << "it < vec.end(): " << (it < vec.end()) << std::endl;  // 1 (true)
}
```

## Common Iterator Operations

```cpp
#include <vector>
#include <iostream>
#include <iterator>  // For std::distance

int main() {
    std::vector<int> numbers = {10, 20, 30, 40, 50};
    auto it = numbers.begin();  // Modern style using auto
    
    // Basic operations
    std::cout << *it << std::endl;    // Dereferencing: 10
    ++it;                             // Move to next element (prefer over it++)
    std::cout << *it << std::endl;    // 20
    
    it += 2;                          // Jump forward by 2 (random access)
    std::cout << *it << std::endl;    // 40
    
    --it;                             // Move backward
    std::cout << *it << std::endl;    // 30
    
    // Iterator arithmetic (for random access iterators)
    auto it2 = numbers.begin();
    std::cout << "Distance: " << it - it2 << std::endl;  // 2
    
    // For non-random access iterators, use std::distance
    std::cout << "Distance: " << std::distance(it2, it) << std::endl;  // 2
    
    // Comparing iterators
    if (it != numbers.end()) {
        std::cout << "Not at the end yet\n";
    }
    
    // Advancing iterators (works for all iterator types)
    std::advance(it, 1);  // Move forward 1 step to 40
    std::cout << *it << std::endl;
    
    // Getting next/prev iterators (C++11)
    auto next_it = std::next(it);      // Iterator to the next element
    auto prev_it = std::prev(it);      // Iterator to the previous element
    
    std::cout << *next_it << std::endl;  // 50
    std::cout << *prev_it << std::endl;  // 30
}
```

## Container-Specific Iterators

Different containers provide specific types of iterators:

```cpp
#include <vector>
#include <list>
#include <map>
#include <set>
#include <unordered_map>
#include <iostream>

int main() {
    // Vector - random access
    std::vector<int> vec = {1, 2, 3};
    std::vector<int>::iterator vec_it = vec.begin();
    vec_it += 2;  // Random access - jumps directly to element 3
    std::cout << "Vector element: " << *vec_it << std::endl;
    
    // List - bidirectional only
    std::list<int> lst = {1, 2, 3};
    std::list<int>::iterator lst_it = lst.begin();
    ++lst_it;     // Forward one step
    --lst_it;     // Backward one step
    std::cout << "List element: " << *lst_it << std::endl;
    // lst_it += 2;  // Error! List iterators don't support random access
    
    // Set - bidirectional, elements are const
    std::set<int> s = {3, 1, 4};  // Sorted automatically: 1, 3, 4
    auto set_it = s.begin();
    std::cout << "First set element: " << *set_it << std::endl;  // 1
    // *set_it = 5;  // Error! Can't modify set elements through iterator
    
    // Map - bidirectional
    std::map<std::string, int> ages = {{"Alice", 25}, {"Bob", 30}, {"Charlie", 35}};
    std::map<std::string, int>::iterator map_it = ages.begin();
    // Maps iterate through key-value pairs as std::pair objects
    std::cout << map_it->first << ": " << map_it->second << std::endl;
    // Output: Alice: 25 (maps are sorted by key)
    
    // Increment to next entry
    ++map_it;
    std::cout << map_it->first << ": " << map_it->second << std::endl;
    // Output: Bob: 30
    
    // Modifying map values (but not keys)
    map_it->second = 31;  // Change Bob's age
    
    // Unordered containers - forward iterators
    std::unordered_map<std::string, int> hash_map = {{"X", 1}, {"Y", 2}};
    // Order not guaranteed
    for (auto it = hash_map.begin(); it != hash_map.end(); ++it) {
        std::cout << it->first << ": " << it->second << std::endl;
    }
}
```

## Const Iterators

When you only need read access, use `const_iterator` for safety:

```cpp
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    
    // Three ways to get const iterators:
    
    // 1. From non-const container using cbegin()/cend() (C++11)
    auto it1 = numbers.cbegin();
    
    // 2. Explicitly declare const_iterator type
    std::vector<int>::const_iterator it2 = numbers.begin();
    
    // 3. From const container (all iterators are const)
    const std::vector<int>& const_numbers = numbers;
    auto it3 = const_numbers.begin();  // const_iterator automatically
    
    // Read but can't modify
    std::cout << *it1 << std::endl;  // OK
    // *it1 = 10;  // Error! Cannot modify through const_iterator
    
    // Traversing with const_iterator
    for (auto it = numbers.cbegin(); it != numbers.cend(); ++it) {
        std::cout << *it << " ";
        // *it += 1;  // Error! Cannot modify through const_iterator
    }
    
    // Modern C++11 range-based for loop with const reference
    // (implicitly uses const iterators)
    for (const int& num : numbers) {
        std::cout << num << " ";
        // num = 10;  // Error! Can't modify through const reference
    }
}
```

## Reverse Iterators

To traverse containers in reverse order:

```cpp
#include <vector>
#include <list>
#include <iostream>
#include <algorithm>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    
    // Using reverse iterators
    std::cout << "Reverse traversal: ";
    for (auto rit = numbers.rbegin(); rit != numbers.rend(); ++rit) {
        std::cout << *rit << " ";
    }
    std::cout << std::endl;
    // Output: 5 4 3 2 1
    
    // Explicitly typed reverse iterator
    std::vector<int>::reverse_iterator rev_it = numbers.rbegin();
    std::cout << "First element from end: " << *rev_it << std::endl;  // 5
    
    // Const reverse iterator
    std::vector<int>::const_reverse_iterator const_rev_it = numbers.crbegin();
    std::cout << "Using const reverse iterator: " << *const_rev_it << std::endl;
    
    // Converting reverse iterator to forward iterator with base()
    auto normal_it = (++numbers.rbegin()).base();
    std::cout << "Element via base(): " << *normal_it << std::endl;  // 4
    
    // Using reverse iterators with algorithms
    std::vector<int> reversed(numbers.size());
    std::copy(numbers.rbegin(), numbers.rend(), reversed.begin());
    
    std::cout << "Reversed vector: ";
    for (int num : reversed) {
        std::cout << num << " ";  // 5 4 3 2 1
    }
    std::cout << std::endl;
}
```

## Iterator Invalidation

Be careful about iterator invalidation when modifying containers:

```cpp
#include <vector>
#include <list>
#include <iostream>

int main() {
    // Vector iterator invalidation examples
    std::vector<int> vec = {1, 2, 3, 4, 5};
    auto it = vec.begin() + 2;  // Points to 3
    std::cout << "Before modification: " << *it << std::endl;  // 3
    
    // Adding elements (may cause reallocation)
    vec.push_back(6);  // May invalidate ALL iterators if reallocation occurs
    
    // DANGER: 'it' may now be invalid!
    // std::cout << *it << std::endl;  // Undefined behavior!
    
    // Safe: reacquire iterator after modification
    it = vec.begin() + 2;
    std::cout << "After reacquiring: " << *it << std::endl;  // 3
    
    // Erasing elements invalidates iterators to erased elements and beyond
    auto erase_it = vec.begin() + 1;  // Points to 2
    vec.erase(erase_it);  // Removes 2, invalidates erase_it and all following iterators
    
    // Safe: iterator returned by erase
    erase_it = vec.erase(vec.begin());  // Removes 1, returns iterator to next element
    std::cout << "After erase: " << *erase_it << std::endl;  // 3
    
    // List has more stable iterators
    std::list<int> lst = {10, 20, 30, 40, 50};
    auto lst_it = lst.begin();
    ++lst_it;  // Points to 20
    ++lst_it;  // Points to 30
    std::cout << "List iterator: " << *lst_it << std::endl;  // 30
    
    // List insertion doesn't invalidate existing iterators
    lst.push_back(60);
    lst.push_front(5);
    std::cout << "Still valid: " << *lst_it << std::endl;  // Still 30
    
    // List erasure only invalidates iterators to erased elements
    auto another_it = lst.begin();
    ++another_it;  // Points to 10
    lst.erase(another_it);  // Only invalidates the iterator to 10
    std::cout << "Still valid after erase: " << *lst_it << std::endl;  // Still 30
}
```

## Modern C++ Range-Based For Loops

C++11 introduced range-based for loops as a simpler way to iterate:

```cpp
#include <vector>
#include <map>
#include <iostream>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    
    // Simple element access (copies elements)
    std::cout << "By value: ";
    for (int number : numbers) {
        std::cout << number << " ";
        // Modifying number doesn't affect the container
        number *= 2;
    }
    std::cout << std::endl;
    
    // Modifying elements using reference
    std::cout << "By reference: ";
    for (int& number : numbers) {
        number *= 2;  // Modifies the actual container elements
        std::cout << number << " ";  // 2 4 6 8 10
    }
    std::cout << std::endl;
    
    // Read-only access with const reference (most efficient)
    std::cout << "By const reference: ";
    for (const int& number : numbers) {
        std::cout << number << " ";  // 2 4 6 8 10
        // number = 1;  // Error! Can't modify a const reference
    }
    std::cout << std::endl;
    
    // Works with all container types
    std::map<std::string, int> ages = {{"Alice", 25}, {"Bob", 30}, {"Charlie", 35}};
    
    for (const auto& pair : ages) {
        std::cout << pair.first << " is " << pair.second << " years old." << std::endl;
    }
    
    // C++17 structured binding for map iteration
    for (const auto& [name, age] : ages) {
        std::cout << name << " is " << age << " years old." << std::endl;
    }
}
```

## Iterator Algorithms with `<algorithm>`

The standard library provides powerful algorithms using iterators:

```cpp
#include <vector>
#include <algorithm>
#include <numeric>
#include <iostream>
#include <iterator>

int main() {
    std::vector<int> numbers = {5, 2, 8, 1, 9};
    
    // Sorting
    std::sort(numbers.begin(), numbers.end());
    std::cout << "Sorted: ";
    for (int n : numbers) std::cout << n << " ";  // 1 2 5 8 9
    std::cout << std::endl;
    
    // Finding elements
    auto it = std::find(numbers.begin(), numbers.end(), 5);
    if (it != numbers.end()) {
        std::cout << "Found 5 at position: " 
                  << std::distance(numbers.begin(), it) << std::endl;  // 2
    }
    
    // Counting elements matching a condition
    int count = std::count_if(numbers.begin(), numbers.end(), 
                             [](int x) { return x % 2 == 0; });
    std::cout << "Even numbers: " << count << std::endl;  // 1 (only 2 is even)
    
    // Transforming elements
    std::vector<int> squares(numbers.size());
    std::transform(numbers.begin(), numbers.end(), squares.begin(),
                  [](int x) { return x * x; });
    
    std::cout << "Squares: ";
    for (int n : squares) std::cout << n << " ";  // 1 4 25 64 81
    std::cout << std::endl;
    
    // Accumulating values
    int sum = std::accumulate(numbers.begin(), numbers.end(), 0);
    std::cout << "Sum: " << sum << std::endl;  // 25
    
    // Filtering with back_inserter
    std::vector<int> even_nums;
    std::copy_if(numbers.begin(), numbers.end(), 
                std::back_inserter(even_nums),
                [](int x) { return x % 2 == 0; });
    
    std::cout << "Even: ";
    for (int n : even_nums) std::cout << n << " ";  // 2 8
    std::cout << std::endl;
    
    // Using inserter with sets
    std::set<int> num_set;
    std::copy(numbers.begin(), numbers.end(), 
             std::inserter(num_set, num_set.end()));
    
    // Using direct output
    std::cout << "To output: ";
    std::copy(numbers.begin(), numbers.end(), 
             std::ostream_iterator<int>(std::cout, " "));
    std::cout << std::endl;
}
```

## Iterator Adaptors

C++ provides several iterator adaptors to enhance functionality:

```cpp
#include <vector>
#include <list>
#include <iterator>
#include <iostream>
#include <algorithm>

int main() {
    std::vector<int> vec;
    
    // back_inserter - adds elements to the end of a container
    std::generate_n(std::back_inserter(vec), 5, 
                   [n = 0]() mutable { return ++n; });
    
    std::cout << "After back_inserter: ";
    for (int n : vec) std::cout << n << " ";  // 1 2 3 4 5
    std::cout << std::endl;
    
    // front_inserter - adds elements to the beginning (not for vector)
    std::list<int> lst;
    std::generate_n(std::front_inserter(lst), 3, 
                   [n = 10]() mutable { return n--; });
    
    std::cout << "After front_inserter: ";
    for (int n : lst) std::cout << n << " ";  // 8 9 10
    std::cout << std::endl;
    
    // inserter - inserts at a specific position
    std::vector<int> dest = {1, 5};
    std::vector<int> source = {2, 3, 4};
    
    // Insert source elements into dest at position 1
    std::copy(source.begin(), source.end(), 
             std::inserter(dest, dest.begin() + 1));
    
    std::cout << "After inserter: ";
    for (int n : dest) std::cout << n << " ";  // 1 2 3 4 5
    std::cout << std::endl;
    
    // move_iterator - moves elements instead of copying
    std::vector<std::string> src = {"Hello", "World"};
    std::vector<std::string> dst;
    
    // Move strings instead of copying them
    std::copy(std::make_move_iterator(src.begin()),
             std::make_move_iterator(src.end()),
             std::back_inserter(dst));
    
    std::cout << "dst[0]: " << dst[0] << std::endl;  // Hello
    std::cout << "src[0] empty? " << src[0].empty() << std::endl;  // Likely 1 (true)
}
```

## Custom Iterators

You can create your own iterators for custom containers:

```cpp
#include <iterator>
#include <iostream>
#include <algorithm>

// Simple custom container
class IntContainer {
private:
    int data[10];
    
public:
    IntContainer() {
        for (int i = 0; i < 10; i++) data[i] = i;
    }
    
    // Custom iterator class
    class Iterator {
    private:
        int* ptr;
        
    public:
        // Iterator traits (required for algorithm compatibility)
        using iterator_category = std::random_access_iterator_tag;
        using value_type = int;
        using difference_type = std::ptrdiff_t;
        using pointer = int*;
        using reference = int&;
        
        // Constructors
        Iterator(int* p) : ptr(p) {}
        
        // Basic operators
        reference operator*() const { return *ptr; }
        pointer operator->() const { return ptr; }
        
        // Increment/decrement
        Iterator& operator++() { ++ptr; return *this; }  // Pre-increment
        Iterator operator++(int) { Iterator tmp = *this; ++ptr; return tmp; }  // Post-increment
        
        Iterator& operator--() { --ptr; return *this; }  // Pre-decrement
        Iterator operator--(int) { Iterator tmp = *this; --ptr; return tmp; }  // Post-decrement
        
        // Random access operators
        Iterator& operator+=(difference_type n) { ptr += n; return *this; }
        Iterator operator+(difference_type n) const { return Iterator(ptr + n); }
        
        Iterator& operator-=(difference_type n) { ptr -= n; return *this; }
        Iterator operator-(difference_type n) const { return Iterator(ptr - n); }
        
        difference_type operator-(const Iterator& other) const {
            return ptr - other.ptr;
        }
        
        reference operator[](difference_type n) const { return ptr[n]; }
        
        // Comparison operators
        friend bool operator==(const Iterator& a, const Iterator& b) {
            return a.ptr == b.ptr;
        }
        friend bool operator!=(const Iterator& a, const Iterator& b) {
            return a.ptr != b.ptr;
        }
        
        friend bool operator<(const Iterator& a, const Iterator& b) {
            return a.ptr < b.ptr;
        }
        friend bool operator>(const Iterator& a, const Iterator& b) {
            return a.ptr > b.ptr;
        }
        friend bool operator<=(const Iterator& a, const Iterator& b) {
            return a.ptr <= b.ptr;
        }
        friend bool operator>=(const Iterator& a, const Iterator& b) {
            return a.ptr >= b.ptr;
        }
        
        friend Iterator operator+(difference_type n, const Iterator& it) {
            return Iterator(it.ptr + n);
        }
    };
    
    // Iterator factory methods
    Iterator begin() { return Iterator(&data[0]); }
    Iterator end() { return Iterator(&data[10]); }
    
    // Const iterator version
    class ConstIterator {
    private:
        const int* ptr;
        
    public:
        // Same traits but with const pointer/reference
        using iterator_category = std::random_access_iterator_tag;
        using value_type = int;
        using difference_type = std::ptrdiff_t;
        using pointer = const int*;
        using reference = const int&;
        
        ConstIterator(const int* p) : ptr(p) {}
        
        // Read-only access
        reference operator*() const { return *ptr; }
        pointer operator->() const { return ptr; }
        
        // Other operators (similar to non-const version)
        ConstIterator& operator++() { ++ptr; return *this; }
        // ... additional operators omitted for brevity
    };
    
    // Const iterator factory methods
    ConstIterator begin() const { return ConstIterator(&data[0]); }
    ConstIterator end() const { return ConstIterator(&data[10]); }
};

int main() {
    IntContainer container;
    
    // Using our custom iterator
    std::cout << "Forward: ";
    for (auto it = container.begin(); it != container.end(); ++it) {
        std::cout << *it << " ";
    }
    std::cout << std::endl;
    // Output: 0 1 2 3 4 5 6 7 8 9
    
    // Random access
    auto it = container.begin();
    it += 5;
    std::cout << "Element at index 5: " << *it << std::endl;  // 5
    
    // Using STL algorithms with custom iterator
    std::cout << "Sum: " << 
        std::accumulate(container.begin(), container.end(), 0) << std::endl;  // 45
    
    // Range-based for loop works if begin/end are properly defined
    std::cout << "Range-based for: ";
    for (int val : container) {
        std::cout << val << " ";
    }
    std::cout << std::endl;
}
```

## Iterator Best Practices

1. **Use the appropriate iterator type for your needs**
   - `const_iterator` for read-only access
   - Regular iterators when you need to modify elements

2. **Be aware of iterator invalidation rules**
   - Vector: Insertion/deletion invalidates iterators to elements at or after the point of insertion/deletion
   - List/Set/Map: Insertion doesn't invalidate existing iterators; deletion invalidates only iterators to the deleted elements

3. **Prefer modern C++ idioms**
   - Use `auto` to simplify iterator declarations
   - Use range-based for loops for simple iteration
   - Use algorithms from `<algorithm>` header with iterators

4. **Performance considerations**
   - Prefer prefix increment (`++it`) over postfix (`it++`) for better performance
   - Use `reserve()` on vectors before adding elements to prevent iterator invalidation
   - Pass large objects by const reference in range-based loops

5. **Safety tips**
   - Always check if iterator is valid before dereferencing (not equal to `.end()`)
   - Be cautious with iterator arithmetic to avoid going out of bounds
   - If modifying a container, reacquire iterators after operations that may invalidate them

6. **Iterator adapters**
   - Use `std::back_inserter` and other inserters for flexible algorithms
   - Consider using iterator adapters for move semantics with `std::make_move_iterator`

7. **Debug techniques**
   - Check distances between iterators with `std::distance`
   - Convert iterators to indices for debugging using `std::distance`