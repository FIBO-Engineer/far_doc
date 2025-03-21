# C++ Return Values

## Basic Return Values

In C++, functions can return values to the caller using the `return` statement. The return type is declared in the function signature.

```cpp
int add(int a, int b) {
    return a + b;  // Returns an integer
}
```

## Return Value Optimization (RVO)

Modern C++ compilers implement Return Value Optimization (RVO) which eliminates unnecessary copies when returning objects from functions.

```cpp
std::vector<int> createVector() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    return vec;  // No copying occurs with RVO
}
```

## Multiple Return Values

### Using std::tuple

```cpp
std::tuple<int, double, std::string> getValues() {
    return {42, 3.14, "hello"};
}

// Usage
auto [num, pi, greeting] = getValues();
```

### Using std::pair

```cpp
std::pair<bool, std::string> findElement(const std::vector<std::string>& vec, const std::string& elem) {
    auto it = std::find(vec.begin(), vec.end(), elem);
    if (it != vec.end()) {
        return {true, *it};
    }
    return {false, ""};
}
```

### Using struct/class

```cpp
struct Result {
    bool success;
    int value;
    std::string error;
};

Result compute() {
    // Computation logic
    return {true, 42, ""};
}
```

## Optional Return Values

Using `std::optional` (C++17):

```cpp
std::optional<int> divideIfPossible(int a, int b) {
    if (b == 0) {
        return std::nullopt;  // No value
    }
    return a / b;
}

// Usage
auto result = divideIfPossible(10, 2);
if (result.has_value()) {
    std::cout << "Result: " << *result << std::endl;
}
```

## Reference Return Values

Functions can return references, but be careful about lifetime issues:

```cpp
// Safe: returning reference to static data
const std::string& getStaticString() {
    static std::string str = "Hello, world!";
    return str;
}

// Dangerous: returning reference to local variable
std::string& getDangerousReference() {
    std::string local = "Danger!";
    return local;  // NEVER DO THIS - local will be destroyed
}
```

## Modifying Values Through References

Instead of returning a value, you can modify variables by passing them as references:

```cpp
void modifyValue(int& value) {
    value = value * 2;  // Modifies the original variable
}

// Usage
int x = 5;
modifyValue(x);  // x is now 10
```

## Returning Shared Pointers

When you need to return dynamically allocated objects with shared ownership:

```cpp
std::shared_ptr<MyClass> createSharedObject() {
    return std::make_shared<MyClass>(/* constructor args */);
}

// Usage
auto obj = createSharedObject();
// No need to manually delete, memory will be freed when last shared_ptr is destroyed
```

## Class Method Return Types and `const`

The `const` qualifier in method signatures affects how you can use and return values in class contexts:

```cpp
class DataContainer {
private:
    std::vector<int> data;
    
public:
    // Non-const method - returns modifiable reference
    std::vector<int>& getData() {
        return data;  // Caller can modify the internal data
    }
    
    // Const method - must return const reference
    const std::vector<int>& getData() const {
        return data;  // Caller cannot modify the internal data
    }
    
    // Value getter that works in both const and non-const contexts
    int getSize() const {
        return data.size();  // Returning by value is always safe
    }
    
    // Modification through reference parameter in non-const context
    void processData(std::vector<int>& result) {
        // Fill result with processed data
        result = data;  // Simplified example
    }
};

// Usage:
DataContainer container;
// Non-const object can use both const and non-const methods
auto& modifiable = container.getData();  // Uses non-const version
modifiable[0] = 100;  // OK - modifies container's data

const DataContainer constContainer;
// Const object can only use const methods
const auto& readOnly = constContainer.getData();  // Uses const version
// readOnly[0] = 100;  // Error - cannot modify const reference
```

## Best Practices

1. Use return value optimization by returning objects directly.
2. Prefer returning by value for small objects.
3. Consider using `std::optional` for functions that might not return a value.
4. Be careful when returning references to ensure the referenced object outlives the reference.
5. For multiple return values, use structured bindings with tuples or custom structs.
6. Use reference parameters (like `void f(Type& param)`) when you need to modify the original variable.
7. Consider `std::shared_ptr` when returning dynamically allocated objects that need shared ownership.