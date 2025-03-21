# C++ Containers: A Comprehensive Guide

## Introduction
C++ provides a powerful set of container classes within the Standard Template Library (STL). These containers help manage collections of objects efficiently. Containers in C++ are broadly classified into **sequence containers**, **associative containers**, and **unordered containers**.

---

## 1. Sequence Containers
Sequence containers store elements in a linear fashion. The most commonly used sequence containers are:

### 1.1 **std::vector**
A dynamic array that grows as needed. It provides fast random access and efficient insertion/removal at the end. The underlying implementation uses a **dynamic array**, allowing O(1) access by index but requiring O(n) time for insertions or deletions in the middle.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(1) |
| Insert at end     | Amortized O(1) |
| Insert at middle  | O(n) |
| Delete at end     | O(1) |
| Delete at middle  | O(n) |
| Get size          | O(1) |

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};
    v.push_back(6);

    for (int num : v) {
        std::cout << num << " ";
    }
    return 0;
}
```

**Use Case:**
- Best for **large datasets** that require frequent random access and occasional resizing.
- Used in **graphics processing, dynamic arrays, and buffer storage**.

### 1.2 **std::list**
A doubly linked list that allows efficient insertion and deletion at any position. It provides O(1) insertions and deletions using iterators but requires O(n) time for index-based access.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(n) |
| Insert at end     | O(1) |
| Insert at middle  | O(1) with iterator |
| Delete at end     | O(1) |
| Delete at middle  | O(1) with iterator |
| Get size          | O(1) |

```cpp
#include <iostream>
#include <list>

int main() {
    std::list<int> lst = {1, 2, 3};
    lst.push_front(0);
    lst.push_back(4);

    for (int num : lst) {
        std::cout << num << " ";
    }
    return 0;
}
```

**Use Case:**
- Ideal when frequent **insertions or deletions in the middle** are needed.
- Used in **task scheduling, undo functionality, and managing ordered elements dynamically**.

### 1.3 **std::deque**
A double-ended queue that supports fast insertions and deletions at both ends. Unlike `vector`, `deque` is implemented as a **dynamic array of blocks**, ensuring O(1) access but with additional memory overhead.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(1) |
| Insert at end     | O(1) |
| Insert at middle  | O(n) |
| Delete at end     | O(1) |
| Delete at middle  | O(n) |
| Get size          | O(1) |

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<int> dq = {2, 3, 4};
    dq.push_front(1);
    dq.push_back(5);

    for (int num : dq) {
        std::cout << num << " ";
    }
    return 0;
}
```

**Use Case:**
- Useful for **double-ended operations** where insertions and deletions are needed from both ends.
- Common in **browser history tracking, task processing, and caching systems**.

---

## 2. Associative Containers
Associative containers store elements in a sorted order based on keys.

### 2.1 **std::set**
A sorted collection of unique elements. Implemented as a **Red-Black Tree**, allowing efficient O(log n) insertions, deletions, and lookups but no direct indexing.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(n) |
| Insert at end     | N/A |
| Insert at middle  | O(log n) |
| Delete at end     | N/A |
| Delete at middle  | O(log n) |
| Get size          | O(1) |

```cpp
#include <iostream>
#include <set>

int main() {
    std::set<int> s = {4, 2, 5, 1, 3};
    s.insert(3);

    for (int num : s) {
        std::cout << num << " ";
    }
    return 0;
}
```

**Use Case:**
- Useful for maintaining **unique elements in sorted order**.
- Used in **leaderboards, unique word counting, and managing sorted records**.

### 2.2 **std::map**
A key-value pair container sorted by keys. Implemented as a **Red-Black Tree**, similar to `set`, providing O(log n) operations for insertions, deletions, and lookups.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(log n) |
| Insert at end     | N/A |
| Insert at middle  | O(log n) |
| Delete at end     | N/A |
| Delete at middle  | O(log n) |
| Get size          | O(1) |


```cpp
#include <iostream>
#include <map>

int main() {
    std::map<int, std::string> m;
    m[1] = "one";
    m[2] = "two";
    m[3] = "three";

    for (const auto& [key, value] : m) {
        std::cout << key << ": " << value << "\n";
    }
    return 0;
}
```

**Use Case:**
- Best when a **sorted dictionary-like structure** is needed.
- Used in **configuration settings, caching mechanisms, and associative data storage**.

---

## 3. Unordered Containers
Unordered containers use **hash tables** for fast access and do not maintain order.

### 3.1 **std::unordered_set**
A collection of unique elements with fast lookup. Implemented as a **hash table**, allowing O(1) average time complexity for most operations, but worst-case O(n) due to hash collisions.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | N/A |
| Insert at end     | N/A |
| Insert at middle  | O(1) (average), O(n) (worst) |
| Delete at end     | N/A |
| Delete at middle  | O(1) (average), O(n) (worst) |
| Get size          | O(1) |

**Use Case:**
- Ideal for **quick membership testing and duplicate filtering**.
- Used in **hash-based search, spam filtering, and rapid existence checking**.

### 3.2 **std::unordered_map**
A hash table-based key-value container. Similar to `unordered_set`, it provides O(1) average-time operations but degrades to O(n) in worst-case scenarios due to hash collisions.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(1) (average), O(n) (worst) |
| Insert at end     | N/A |
| Insert at middle  | O(1) (average), O(n) (worst) |
| Delete at end     | N/A |
| Delete at middle  | O(1) (average), O(n) (worst) |
| Get size          | O(1) |

```cpp
#include <iostream>
#include <unordered_map>

int main() {
    std::unordered_map<std::string, int> um;
    um["apple"] = 3;
    um["banana"] = 5;
    um["orange"] = 7;

    for (const auto& [key, value] : um) {
        std::cout << key << ": " << value << "\n";
    }
    return 0;
}
```

**Use Case:**
- Best for **fast key-value lookups** without maintaining order.
- Used in **database indexing, word frequency analysis, and fast data retrieval**.

---

## Understanding the Backends of C++ Containers
Each container in the STL is backed by a specific data structure, optimized for different operations:

- **Dynamic Array (`std::vector`)**: Uses a contiguous memory block, allowing fast index access but costly insertions/deletions in the middle.
- **Doubly Linked List (`std::list`)**: Uses nodes connected via pointers, allowing O(1) insertions/deletions anywhere but O(n) access by index.
- **Dynamic Array of Blocks (`std::deque`)**: A segmented memory model that supports O(1) insertions and deletions at both ends.
- **Red-Black Tree (`std::set`, `std::map`)**: A self-balancing binary search tree that ensures O(log n) operations and maintains elements in sorted order.
- **Hash Table (`std::unordered_set`, `std::unordered_map`)**: Uses hashing for O(1) average-time access, but worst-case performance can degrade to O(n) due to collisions.

## Features Introduced in Different C++ Versions

| Feature | Introduced in |
|---------|-------------|
| std::vector, std::list, std::deque, std::set, std::map | C++98 |
| Range-based for loop | C++11 |
| std::unordered_map, std::unordered_set | C++11 |
| Structured bindings (`auto& [key, value]`) | C++17 |


## Benchmark
```cpp
#include <iostream>
#include <vector>
#include <list>
#include <set>
#include <unordered_set>
#include <chrono>
#include <random>

const int ELEMENTS = 100000;

void benchmark_vector() {
    std::vector<int> vec;

    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; ++i) {
        vec.push_back(i);
    }
    auto end = std::chrono::high_resolution_clock::now();
    std::cout << "Vector push_back time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (const auto& val : vec) {
        volatile int x = val;
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Vector iteration time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; i += 1000) {
        volatile int x = vec[i];
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Vector random access time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";
}

void benchmark_list() {
    std::list<int> lst;

    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; ++i) {
        lst.push_back(i);
    }
    auto end = std::chrono::high_resolution_clock::now();
    std::cout << "List push_back time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (const auto& val : lst) {
        volatile int x = val;
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "List iteration time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    auto it = lst.begin();
    for (int i = 0; i < ELEMENTS; i += 1000) {
        std::advance(it, 1000);
        volatile int x = *it;
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "List pseudo-random access time (advance iterator): "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";
}

void benchmark_set() {
    std::set<int> s;

    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; ++i) {
        s.insert(i);
    }
    auto end = std::chrono::high_resolution_clock::now();
    std::cout << "Set insertion time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (const auto& val : s) {
        volatile int x = val;
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Set iteration time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; i += 1000) {
        volatile bool found = s.find(i) != s.end();
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Set lookup time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";
}

void benchmark_unordered_set() {
    std::unordered_set<int> us;

    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; ++i) {
        us.insert(i);
    }
    auto end = std::chrono::high_resolution_clock::now();
    std::cout << "Unordered Set insertion time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (const auto& val : us) {
        volatile int x = val;
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Unordered Set iteration time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";

    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ELEMENTS; i += 1000) {
        volatile bool found = us.find(i) != us.end();
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Unordered Set lookup time: "
              << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count()
              << " µs\n";
}

int main() {
    std::cout << "Benchmarking std::vector vs std::list vs std::set vs std::unordered_set\n";
    
    std::cout << "\n--- std::vector ---\n";
    benchmark_vector();
    
    std::cout << "\n--- std::list ---\n";
    benchmark_list();

    std::cout << "\n--- std::set ---\n";
    benchmark_set();

    std::cout << "\n--- std::unordered_set ---\n";
    benchmark_unordered_set();

    return 0;
}
```