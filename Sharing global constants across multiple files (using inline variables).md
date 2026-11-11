# Sharing Global Constants Across Multiple Files

There are three common ways to share global constants across multiple files in C++: internal-linkage constants, external-linkage constants, and (since C++17) inline variables.

## Global Constants as Internal Variables

Define the constants in a header file. Namespace-scope `constexpr` variables have internal linkage by default, so each translation unit that includes the header gets its own copy.

**`constants.h`**

```cpp
#ifndef CONSTANTS_H
#define CONSTANTS_H

// Define your own namespace to hold constants.
namespace constants
{
    // Global constants have internal linkage by default.
    constexpr double pi{3.14159};
    constexpr double avogadro{6.0221413e23};
    constexpr double myGravity{9.2}; // m/s^2 — gravity is light on this planet
    // ... other related constants
}

#endif
```

**`main.cpp`**

```cpp
#include "constants.h" // Includes a copy of each constant in this file.

#include <iostream>

int main()
{
    std::cout << "Enter a radius: ";
    double radius{};
    std::cin >> radius;

    std::cout << "The circumference is: " << 2 * radius * constants::pi << '\n';

    return 0;
}
```

### Advantages

- Works before C++17.
- Constants can be used in constant expressions in any translation unit that includes the header.

### Disadvantages

- Changing the header requires recompiling every file that includes it.
- Each translation unit that includes the header gets its own copy of each variable.

## Global Constants as External Variables

Declare the constants in a header and define them in one source file. The definitions use `extern constexpr` to give the variables external linkage. The declarations use `extern const`, because a variable cannot be forward-declared as `constexpr`.

**`constants.cpp`**

```cpp
#include "constants.h"

namespace constants
{
    // Use extern to ensure these variables have external linkage.
    extern constexpr double pi{3.14159};
    extern constexpr double avogadro{6.0221413e23};
    extern constexpr double myGravity{9.2}; // m/s^2 — gravity is light on this planet
}
```

**`constants.h`**

```cpp
#ifndef CONSTANTS_H
#define CONSTANTS_H

namespace constants
{
    // Keep these declarations in the same namespace as the definitions.
    // They are declared as const because they cannot be forward-declared as constexpr.
    extern const double pi;
    extern const double avogadro;
    extern const double myGravity;
}

#endif
```

**`main.cpp`**

```cpp
#include "constants.h" // Includes the declarations.

#include <iostream>

int main()
{
    std::cout << "Enter a radius: ";
    double radius{};
    std::cin >> radius;

    std::cout << "The circumference is: " << 2 * radius * constants::pi << '\n';

    return 0;
}
```

### Advantages

- Works before C++17.
- Only one copy of each variable is required.
- If a constant's value changes, only the file containing its definition needs to be recompiled.

### Disadvantages

- The declarations and definitions are in separate files and must be kept in sync.
- Outside the file where they are defined, the variables cannot be used in constant expressions.

## Global Constants as Inline Variables (C++17 and Later)

C++17 inline variables let you define constants in a header while sharing a single variable across translation units.

**`constants.h`**

```cpp
#ifndef CONSTANTS_H
#define CONSTANTS_H

// Define your own namespace to hold constants.
namespace constants
{
    inline constexpr double pi{3.14159};
    inline constexpr double avogadro{6.0221413e23};
    inline constexpr double myGravity{9.2}; // m/s^2 — gravity is light on this planet
    // ... other related constants
}

#endif
```

**`main.cpp`**

```cpp
#include "constants.h"

#include <iostream>

int main()
{
    std::cout << "Enter a radius: ";
    double radius{};
    std::cin >> radius;

    std::cout << "The circumference is: " << 2 * radius * constants::pi << '\n';

    return 0;
}
```

### Advantages

- Constants can be used in constant expressions in any translation unit that includes the header.
- Only one copy of each variable is required.

### Disadvantages

- Requires C++17 or later.
- Changing the header requires recompiling every file that includes it.
