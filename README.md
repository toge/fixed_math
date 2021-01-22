# fixed_math ![MIT](https://img.shields.io/badge/license-MIT-blue.svg)

written from scratch fixed point math library in C++17

## features

* minimum c++17 compiler required
* fixed point 48.16 arithmethic strong type without implicit convertions with assignment and construction, fixed type is safe against unwanted type convertions
* all arithemtic types like float and integral types except double implicitly in base arithemitc operations are promoted to fixed_t
* all arithmetic operations of fixed_t with double type yelds double result type, are promoted and evaluated as double operations
* entire code including trigonometry functions is constexpr \[1\]
* fully header only library as everything is constexpr, see api interface [api](https://github.com/arturbac/fixed_math/blob/master/fixed_lib/include/fixedmath/fixed_math.hpp) and [implementation](https://github.com/arturbac/fixed_math/blob/master/fixed_lib/include/fixedmath/math.h)
* unit tests can be checked at compilation time just including header, see [unittests](https://github.com/arturbac/fixed_math/blob/master/fixed_lib/include/fixedmath/compile_time_unit_tests.h)
* functions that are complement to std math functionality could be optionaly imported to std namespace as overloads for fixed_t type *disabled by default*


\[1\] - By default is used std:sqrt as current cpu's has hardware support for sqrt, but constexpr abacus algorithm could be used defining FIXEDMATH_ENABLE_SQRT_ABACUS_ALGO, which is slower than cpu one

### first performance comparisions of code 0.9.1
At this point code wasn't been optimised, so results are from just from code written with quality only at this point in mind. Results are relative times of computing functions over bigtable of source values in function type (no value convertions)

**Cortex-A73 - Snapdragon 865+**
function | fixed | float | double
---------|-------|-------|------------
sin | 50 ms | 31 ms | 77 ms
asin | 124 ms | 75 ms | 128 ms 
tan | 136 ms | 104 ms | 206 ms 
atan | 113 ms | 110 ms | 165 ms

**Ryzen 9 - 3500X**
function | fixed | float | double
---------|-------|-------|------------
sin | 27ms | 22 ms | 75 ms
asin | 92ms | 58 ms | 106 ms
tan | 81ms | 66 ms | 180 ms
atan | 90ms | 78 ms | 162 ms

## installation

this library is header only except tabelarized trigonometric functions. So If you can use precise trigonometric functions You don't have to build anything.
Just add fixed_lib/include to include path and #include <fixedmath/fixed_math.hpp>. If you want additional inprecise aproxymated functions compile project like any other ordinary CMake project. At this point it doesn't have any tuning parameters for CMake.

## usage
fixed_t is typename of fixed point arithmetic type with common operators like, +, -, * ..

### example

```C++
#include <fixedmath/fixed_math.hpp>

using fixedmath::fixed_t;

//fixed and all functionality is constexpr so You can declare constants see features [1]
inline constexpr fixed_t foo_constant{ fixedmath::tan( 15 * fixedmath::phi/180) };

constexpr fixed_t my_function( fixed_t value )
 {
 using namespace fixedmath;
 return foo_constant + sin(value) / (1.41_fix - 2*cos(value) / 4);
 }

// converting to/from fixed_t
// construction from other arithmetic types is explicit
fixed_t val { 3.14 };
fixed_t val { 3u };
 
//- there is no implicit assignment from other types
float some_float{};
fixed_t some_fixed{};
...
some_fixed = fixed_t{some_float};
 
//- converting to other arithmetic types coud be done with static cast and is explicit
double some_double { static_cast<double>(some_fixed) };
 
// for constant values postfix operator _fix may be used
some_fixed = some_float * 2.45_fix; //operation with float is promoted to fixed_t
some_double = 4.15 * some_fixed; //operation with double is promoted to double

```
## unit tests

To check unit tests just #include <fixedmath/unittests/compile_time_unit_tests.h> in any of Your source file.
or run them wit CMake/CTest "ninja/make test" as they are available in default CMakeConfiguration of source folder see ENABLE_UNIT_TESTS cmake feature i CMakeLists.txt


## version 1.0 goals status

- [x] base arithemtic operations 
- [x] sqrt - abacus algorithm using CLZ ( CLZ is available on x86 and >=armv6, aarch64)  error ~<= 0.000015
- [x] hypot - with normalization to suitable range, to avoid overflow and underflow
- [x] sin, cos - error ~<= 0.0001
- [    ] tan - error on normalized range |x| <= PI/4 ~<= 0.0001, PI/4 |x| <= PI/2 ~<=0.001 exept near 90deg ~<= 0.01
- [x] tan - improve calculations, limit tan__ to |x|<=pi/4
- [x] atan, atan2 - error  ~<= 0.000035
- [x] asin error  ~<= 0.0001
- [    ] remove all old compat code that is compiled
- [    ] cover all functionality with static_assert unit tests
- [x] support clang/gcc c++17 compiler
- [    ] support msvc c++17 compiler

## future goals

- [    ] more math functionality
- [    ] performance comparisions on arm64 and x86 with float/double arithemetic
- [    ] more optimisations, calculation quality enchacments

## Feedback

If you think you have found a bug, please file an issue via issue submission [form](https://github.com/arturbac/fixed_math/issues). Include the relevant information to reproduce the bug for example as static_assert( expression ), if it is important also the compiler version and target architecture. Feature requests and contributions can be filed as issues or pull requests.

## License

This library is available to anybody free of charge, under the terms of MIT License (see LICENSE.md).
