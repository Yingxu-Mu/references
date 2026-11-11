# Global constants as internal variables

```
#ifndef CONSTANTS_H
#define CONSTANTS_H

// Define your own namespace to hold constants
namespace constants
{
    // Global constants have internal linkage by default
    constexpr double pi{3.14159};
    constexpr double avogadro{6.0221413e23};
    constexpr double myGravity{9.2}; // m/s^2 -- gravity is light on this planet
    // ... other related constants
}
#endif
```

```
#include "constants.h" // include a copy of each constant in this file

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

Advantages:

Works prior to C++17.
Can be used in constant expressions in any translation unit that includes them.

Downsides:

Changing anything in the header file requires recompiling files including the header.
Each translation unit including the header gets its own copy of the variable.



# Global constants as external variables

```
#include "constants.h"

namespace constants
{
    // We use extern to ensure these have external linkage
    extern constexpr double pi { 3.14159 };
    extern constexpr double avogadro { 6.0221413e23 };
    extern constexpr double myGravity { 9.2 }; // m/s^2 -- gravity is light on this planet
}
```

```
#ifndef CONSTANTS_H
#define CONSTANTS_H

namespace constants
{
    // Since the actual variables are inside a namespace, the forward declarations need to be inside a namespace as well
    // We can't forward declare variables as constexpr, but we can forward declare them as (runtime) const
    extern const double pi;
    extern const double avogadro;
    extern const double myGravity;
}

#endif
```

```
#include "constants.h" // include all the forward declarations

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

Advantages:

Works prior to C++17.
Only one copy of each variable is required.
Only requires recompilation of one file if the value of a constant changes.

Disadvantages:

Forward declarations and variable definitions are in separate files, and must be kept in sync.
Variables not usable in constant expressions outside of the file in which they are defined.

# Global constants as inline variables C++17

```
#ifndef CONSTANTS_H
#define CONSTANTS_H

// define your own namespace to hold constants
namespace constants
{
    inline constexpr double pi { 3.14159 }; // note: now inline constexpr
    inline constexpr double avogadro { 6.0221413e23 };
    inline constexpr double myGravity { 9.2 }; // m/s^2 -- gravity is light on this planet
    // ... other related constants
}
#endif
```

```
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

Advantages:

Can be used in constant expressions in any translation unit that includes them.
Only one copy of each variable is required.
Downsides:

Only works in C++17 onward.
Changing anything in the header file requires recompiling files including the header.