```

 __    __       _______..______       _______  _______  __      
|  |  |  |     /       ||   _  \     |   ____||   ____||  |     
|  |  |  |    |   (----`|  |_)  |    |  |__   |  |__   |  |     
|  |  |  |     \   \    |      /     |   __|  |   __|  |  |     
|  `--'  | .----)   |   |  |\  \----.|  |____ |  |     |  `----.
 \______/  |_______/    | _| `._____||_______||__|     |_______|
                                                                

```

[![repo-size](https://img.shields.io/github/languages/code-size/Ubpa/USRefl?style=flat)](https://github.com/Ubpa/USRefl/archive/master.zip) [![tag](https://img.shields.io/github/v/tag/Ubpa/USRefl)](https://github.com/Ubpa/USRefl/tags) [![license](https://img.shields.io/github/license/Ubpa/USRefl)](LICENSE) [![compiler explorer](https://img.shields.io/badge/compiler_explorer-online-blue)](https://godbolt.org/z/8r84q7)

⭐ Star us on GitHub — it helps!

# USRefl

> **U**bpa **S**tatic **R**eflection

Header-only, tiny (99 lines) and powerful C++17 static reflection library.

## Feature

- **header-only**, **tiny (99 lines)** and **powerful** ([USRefl_99.h](include/USRefl_99.h))
- **noninvasive** 
- basic
  - (non-static / static) member variable
  - (non-static / static) member function
- **attribute** 
- **enum** 
  - string <-> key
  - static dispatch
- **template** 
- **inheritance** 
  - diamond inheritance
  - iterate bases recursively
  - virtual inheritance

## Example

run it online : [compiler explorer](https://godbolt.org/z/8r84q7) 

```c++
#include <USRefl/USRefl.h>
#include <iostream>
using namespace Ubpa::USRefl;
using namespace std;

// [[...]] act as (structured) command
// they are useless in code
struct [[size(8)]] Point {
  [[not_serialize]]
  float x;
  [[info("hello")]]
  float y;
};

// declaration
template<>
struct TypeInfo<Point> : TypeInfoBase<Point> {
  // if you use <USRefl/USRefl.h>, the name is declared by TypeInfoBase<Point>
  // if you use <USRefl_99.h>, you should declare it here.
  // static constexpr std::string_view name = "struct Point";

  static constexpr FieldList fields = {
    Field{"x", &Point::x, AttrList{ Attr{ "not_serialize" } }},
    Field{"y", &Point::y, AttrList{ Attr{ "info", "hello" } }}
  };

  static constexpr AttrList attrs = {
    Attr{ "size", 8 }
  };
};

int main() {
  TypeInfo<Point>::fields.ForEach([](auto field) {
    cout << field.name << endl;
    field.attrs.ForEach([](auto attr) {
      cout << attr.name;
      if constexpr (attr.has_value)
        cout << ": " << attr.value;
      cout << endl;
    });
  });

  static_assert(TypeInfo<Point>::fields.Contains("x"));

  TypeInfo<Point>::attrs.ForEach([](auto attr) {
    cout << attr.name;
    if constexpr (!attr.has_value)
      cout << ": " << attr.value;
    cout << endl;
  });
  
  TypeInfo<Point>::ForEachVarOf(Point{ 1,2 }, [](auto&& var) {
    cout << var << endl;
  });
}
```

**other example** 

- [template](src/test/01_template/main.cpp) 
- [static](src/test/02_static/main.cpp) 
- [func](src/test/03_func/main.cpp) 
- [enum](src/test/04_enum/main.cpp) 
- [inheritance](src/test/05_inheritance/main.cpp) 
- [virtual inheritance](src/test/07_virtual/main.cpp) 
- [API: accumulate + mask](src/test/08_mask/main.cpp) 
- 99 line: [USRefl_99.h](include/USRefl_99.h)，[test](src/test/06_99/main.cpp), [online](https://godbolt.org/z/hndzze) (test all above)

## Documentation

- [Introduction](doc/intro.md) 

## Integration

- You can choose one of the following three methods

- ⭐ **method 0**: add required file [USRefl_99.h](include/USRefl_99.h) 
- run CMake (download [nameof.hpp](https://github.com/Neargye/nameof/blob/master/include/nameof.hpp) to [include/USRefl/_deps/](include/USRefl/_deps/))
  - **method 1**: directly use [include/USRefl/](include/USRefl/) 
  - ⭐ **method 2**: cmake install, `find package(USRefl REQUIRED)` to get imported target `Ubpa::USRefl_core` 

## Compiler compatibility

- Clang/LLVM >= 6
- GCC >= 7.3
- MSVC++ >= 19.20 / VS 2019 + (not fully support virtual inheritance because of a MSVC++ bug)

## Licensing

You can copy and paste the license summary from below.

```
MIT License

Copyright (c) 2020 Ubpa

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

