# C++ Lambda Functions and Function Binding

## Lambda Functions in C++11

Lambda functions (or lambda expressions) allow you to define anonymous function objects directly at the location where they are needed, typically as arguments to algorithms or custom callbacks.

### Basic Syntax

```cpp
[capture_list](parameters) -> return_type { function_body }
```

- **Capture list**: Specifies which variables from the surrounding scope are accessible within the lambda
- **Parameters**: Function parameters (like regular functions)
- **Return type**: Optional, compiler can deduce it in most cases
- **Function body**: The actual code to execute

### Capture Options

- `[]`: Capture nothing
- `[=]`: Capture all variables by value
- `[&]`: Capture all variables by reference
- `[x, &y]`: Capture x by value, y by reference
- `[=, &z]`: Capture all by value, but z by reference
- `[this]`: Capture the this pointer of the enclosing class

### Examples

#### Simple Lambda

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    
    // Lambda to print each number
    std::for_each(numbers.begin(), numbers.end(), [](int n) {
        std::cout << n << " ";
    });
    // Output: 1 2 3 4 5
    
    return 0;
}
```

#### Lambda with Captures

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    int multiplier = 3;
    int sum = 0;
    
    // Capture multiplier by value and sum by reference
    std::for_each(numbers.begin(), numbers.end(), [multiplier, &sum](int n) {
        sum += n * multiplier;
    });
    
    std::cout << "Sum of all numbers multiplied by " << multiplier << ": " << sum << std::endl;
    // Output: Sum of all numbers multiplied by 3: 45
    
    return 0;
}
```

#### Mutable Lambda

By default, variables captured by value are const. Use the `mutable` keyword to modify them:

```cpp
#include <iostream>

int main() {
    int counter = 0;
    
    // Without mutable (would cause compilation error):
    // auto increment = [counter]() { counter++; return counter; };
    
    // With mutable:
    auto increment = [counter]() mutable { counter++; return counter; };
    
    std::cout << increment() << std::endl; // Output: 1
    std::cout << increment() << std::endl; // Output: 2
    std::cout << "Original counter: " << counter << std::endl; // Output: Original counter: 0
    
    return 0;
}
```

## Function Binding in C++

Function binding creates function objects that invoke a target function with some arguments bound to specific values. There are several approaches to function binding in C++:

### 1. Function Pointers

The traditional way to refer to functions. They can point to functions but don't store state or bind arguments.

```cpp
#include <iostream>

// Function to be pointed to
int add(int a, int b) {
    return a + b;
}

// Function that accepts a function pointer
int apply(int (*func)(int, int), int x, int y) {
    return func(x, y);
}

int main() {
    // Create a function pointer
    int (*funcPtr)(int, int) = add;
    
    // Use the function pointer directly
    std::cout << "Direct call: " << funcPtr(5, 3) << std::endl;  // Output: 8
    
    // Pass the function pointer to another function
    std::cout << "Through apply: " << apply(funcPtr, 5, 3) << std::endl;  // Output: 8
    
    return 0;
}
```

Function pointers have limitations:
- Cannot bind arguments (no partial application)
- Cannot store state
- Cannot point to member functions directly (require different syntax)

### 2. Function Objects (Functors)

Classes that overload the `operator()` to make instances callable like functions. They can store state.

```cpp
#include <iostream>

// A functor with state
class Adder {
private:
    int base;
    
public:
    Adder(int base) : base(base) {}
    
    // Call operator
    int operator()(int x) const {
        return base + x;
    }
};

int main() {
    // Create a functor with base 10
    Adder add10(10);
    
    // Use the functor
    std::cout << "10 + 5 = " << add10(5) << std::endl;  // Output: 15
    
    return 0;
}
```

### 3. Member Function Pointers

Special pointers that can point to class member functions. They are more complex to use than regular function pointers.

```cpp
#include <iostream>

class Calculator {
public:
    int add(int a, int b) {
        return a + b;
    }
    
    int subtract(int a, int b) {
        return a - b;
    }
};

int main() {
    // Create a member function pointer
    int (Calculator::*operation)(int, int) = &Calculator::add;
    
    // Create an instance of the class
    Calculator calc;
    
    // Call the member function through the pointer
    std::cout << "Result: " << (calc.*operation)(5, 3) << std::endl;  // Output: 8
    
    // Change the pointer to point to a different member function
    operation = &Calculator::subtract;
    std::cout << "Result: " << (calc.*operation)(5, 3) << std::endl;  // Output: 2
    
    return 0;
}
```

### 4. std::bind

Introduced in C++11, `std::bind` creates a new callable object that forwards its arguments to another callable after transforming them according to specified rules.

```cpp
#include <functional>
#include <iostream>

void print(int a, int b, int c) {
    std::cout << a << ", " << b << ", " << c << std::endl;
}

int main() {
    using namespace std::placeholders; // For _1, _2, etc.
    
    // Bind the first parameter to 10
    auto bound1 = std::bind(print, 10, _1, _2);
    bound1(20, 30); // Output: 10, 20, 30
    
    // Reorder parameters
    auto bound2 = std::bind(print, _2, _3, _1);
    bound2(100, 200, 300); // Output: 200, 300, 100
    
    // Bind all parameters
    auto bound3 = std::bind(print, 1, 2, 3);
    bound3(); // Output: 1, 2, 3
    
    return 0;
}
```

### 5. std::function

Introduced in C++11, `std::function` is a general-purpose polymorphic function wrapper that can store, copy, and invoke any callable object.

```cpp
#include <functional>
#include <iostream>

// Regular function
int add(int a, int b) {
    return a + b;
}

// Function object (functor)
struct Multiply {
    int operator()(int a, int b) const {
        return a * b;
    }
};

int main() {
    // Store regular function
    std::function<int(int, int)> func1 = add;
    std::cout << "func1(2, 3): " << func1(2, 3) << std::endl; // Output: 5
    
    // Store function object
    std::function<int(int, int)> func2 = Multiply();
    std::cout << "func2(2, 3): " << func2(2, 3) << std::endl; // Output: 6
    
    // Store lambda
    std::function<int(int, int)> func3 = [](int a, int b) { return a - b; };
    std::cout << "func3(5, 3): " << func3(5, 3) << std::endl; // Output: 2
    
    // Store the result of a bind expression
    using namespace std::placeholders;
    std::function<int(int)> func4 = std::bind(add, _1, 5);
    std::cout << "func4(10): " << func4(10) << std::endl; // Output: 15
    
    return 0;
}
```

### 6. Lambda Functions

Lambda functions provide a concise way to create function objects. They can capture context and are particularly useful for short, one-off functions.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    int base = 10;
    
    // Lambda that captures base by value
    auto add = [base](int x) { return base + x; };
    
    std::cout << "10 + 5 = " << add(5) << std::endl;  // Output: 15
    
    // Lambda without captures
    auto multiply = [](int x, int y) { return x * y; };
    
    std::cout << "3 * 4 = " << multiply(3, 4) << std::endl;  // Output: 12
    
    return 0;
}
```

### 7. std::mem_fn (C++11)

Adapts a member function to be used with standard algorithms and function objects.

```cpp
#include <iostream>
#include <functional>
#include <vector>
#include <algorithm>

class Person {
private:
    std::string name;
    int age;
    
public:
    Person(const std::string& name, int age) : name(name), age(age) {}
    
    int getAge() const { return age; }
    std::string getName() const { return name; }
};

int main() {
    std::vector<Person> people = {
        Person("Alice", 25),
        Person("Bob", 30),
        Person("Charlie", 20)
    };
    
    // Use mem_fn to create a callable that invokes getAge() on each object
    std::vector<int> ages;
    std::transform(people.begin(), people.end(), 
                  std::back_inserter(ages),
                  std::mem_fn(&Person::getAge));
    
    for (int age : ages) {
        std::cout << "Age: " << age << std::endl;
    }
    
    return 0;
}
```

### 8. std::invoke (C++17)

A unified way to call functions, member functions, and callable objects.

```cpp
#include <iostream>
#include <functional>

struct Foo {
    void display() const {
        std::cout << "Foo::display()" << std::endl;
    }
    
    int add(int a, int b) const {
        return a + b;
    }
};

void globalFunc(int x) {
    std::cout << "Global function: " << x << std::endl;
}

int main() {
    // Invoke a free function
    std::invoke(globalFunc, 42);  // Output: Global function: 42
    
    Foo foo;
    
    // Invoke a member function
    std::invoke(&Foo::display, foo);  // Output: Foo::display()
    
    // Invoke a member function with arguments
    int result = std::invoke(&Foo::add, foo, 10, 20);
    std::cout << "Result: " << result << std::endl;  // Output: Result: 30
    
    // Invoke a lambda
    auto lambda = [](int x) { return x * x; };
    std::cout << "Lambda result: " << std::invoke(lambda, 5) << std::endl;  // Output: Lambda result: 25
    
    return 0;
}
```

### Combining Lambda and Bind

```cpp
#include <functional>
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    int threshold = 3;
    
    // Using lambda
    auto countWithLambda = std::count_if(numbers.begin(), numbers.end(), 
        [threshold](int n) { return n > threshold; });
    
    // Using bind with a lambda
    auto isGreaterThan = [](int n, int t) { return n > t; };
    auto countWithBind = std::count_if(numbers.begin(), numbers.end(),
        std::bind(isGreaterThan, std::placeholders::_1, threshold));
    
    std::cout << "Count with lambda: " << countWithLambda << std::endl; // Output: 2
    std::cout << "Count with bind: " << countWithBind << std::endl;     // Output: 2
    
    return 0;
}
```

### Comparison of Function Binding Methods

| Method | Introduced | Advantages | Limitations |
|--------|------------|------------|-------------|
| Function Pointers | C | Simple, low overhead | No state, no partial application |
| Functors | C++ | Can store state, customizable | Requires defining a class |
| Member Function Pointers | C++ | Can call class methods | Complex syntax, need class instance |
| std::bind | C++11 | Argument binding, reordering | More verbose than lambdas |
| std::function | C++11 | Type erasure, generic wrapper | Some overhead compared to direct calls |
| Lambda Functions | C++11 | Concise, captures context | Limited to their defined scope |
| std::mem_fn | C++11 | Simplifies using member functions | Only for member functions |
| std::invoke | C++17 | Unified calling syntax | Newer standard, may not be available |

### Best Practices

- **Modern C++ approach**: Prefer lambdas for simple, local functions and std::function for more complex scenarios
- **Performance**: For performance-critical code, function pointers or functors might have less overhead
- **Readability**: Choose the method that makes your code most readable for the specific use case
- **Flexibility**: std::function provides the most flexibility but with some performance cost
- **C++17 and beyond**: Consider using std::invoke for a unified approach

Both lambda expressions and function binding are powerful tools in modern C++ that make functional programming techniques more accessible and code more expressive.