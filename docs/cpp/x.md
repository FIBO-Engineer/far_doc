# C++ Containers: A Comprehensive Guide

## Introduction
C++ provides a powerful set of container classes within the Standard Template Library (STL). These containers help manage collections of objects efficiently. Containers in C++ are broadly classified into **sequence containers**, **associative containers**, and **unordered containers**.

---

## 1. Sequence Containers
Sequence containers store elements in a linear fashion and allow efficient traversal.

### 1.1 **std::vector** (C++98)
A dynamic array that grows as needed. It provides fast random access and efficient insertion/removal at the end.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(1) |
| Insert at end     | Amortized O(1) |
| Insert at middle  | O(n) |
| Delete at end     | O(1) |
| Delete at middle  | O(n) |
| Get size          | O(1) |

**Example:**
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

### 1.2 **std::list** (C++98)
A doubly linked list that allows efficient insertion and deletion at any position.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(n) |
| Insert at end     | O(1) |
| Insert at middle  | O(1) with iterator |
| Delete at end     | O(1) |
| Delete at middle  | O(1) with iterator |
| Get size          | O(1) |

**Example:**
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

### 1.3 **std::deque** (C++98)
A double-ended queue that supports fast insertions and deletions at both ends.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(1) |
| Insert at end     | O(1) |
| Insert at middle  | O(n) |
| Delete at end     | O(1) |
| Delete at middle  | O(n) |
| Get size          | O(1) |

**Example:**
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

---

## 2. Associative Containers
Associative containers store elements in a sorted order based on keys.

### 2.1 **std::set** (C++98)
A sorted collection of unique elements.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(n) |
| Insert at end     | N/A |
| Insert at middle  | O(log n) |
| Delete at end     | N/A |
| Delete at middle  | O(log n) |
| Get size          | O(1) |

**Example:**
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

### 2.2 **std::map** (C++98)
A key-value pair container sorted by keys.

| Operation          | Time Complexity |
|--------------------|----------------|
| Access (by index) | O(log n) |
| Insert at end     | N/A |
| Insert at middle  | O(log n) |
| Delete at end     | N/A |
| Delete at middle  | O(log n) |
| Get size          | O(1) |

**Example:**
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

---

## 3. Unordered Containers
Unordered containers use **hash tables** for fast access and do not maintain order. Introduced in **C++11**.

### 3.1 **std::unordered_set** (C++11)
A collection of unique elements with fast lookup.

### 3.2 **std::unordered_map** (C++11)
A hash table-based key-value container.

**Example:**
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

---



By understanding these features and their time complexities, you can select the right container for your application to maximize efficiency and performance.

