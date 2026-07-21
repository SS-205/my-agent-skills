# C++ Library Project — Coding Conventions

## 1. Header File Guards

Use `#ifndef` / `#define` / `#endif` style with uppercase path-based names:

```cpp
#ifndef PROJECTNAME_MODULENAME_CLASSNAME_H
#define PROJECTNAME_MODULENAME_CLASSNAME_H
// ...
#endif
```

## 2. Namespace Conventions

| Scope | Namespace |
|-------|-----------|
| Core library | `projectname` (lowercase, matching project name) |
| Math utilities | `projectname::math` |
| Sub-modules | `projectname::module` |

Use nested namespace syntax (C++17 style):

```cpp
namespace projectname::math {
// ...
}
```

## 3. Naming Rules

| Element | Convention | Example |
|---------|------------|---------|
| Classes / Structs | PascalCase | `ABLine`, `GeoPoint`, `GuidelineBase` |
| Methods / Functions | camelCase | `setPoints()`, `compute()`, `headingRad()` |
| Parameters | camelCase | `startPoint`, `endPoint` |
| Member variables | snake_case with trailing underscore | `a_`, `b_`, `valid_` |
| Constants | `k` prefix + PascalCase | `kVersionMajor`, `kPi` |
| Enums | `enum class` (scoped) | `enum class Type { ABLine, Curve }` |
| Macros | UPPER_SNAKE_CASE | `PROJECTNAME_API` |
| File names | PascalCase matching class | `ABLine.h`, `GeoPoint.cpp` |

## 4. Export Macro Pattern

Create `export.h` with platform-specific export/import macros:

```cpp
#ifndef PROJECTNAME_EXPORT_H
#define PROJECTNAME_EXPORT_H

#if defined(_MSC_VER) || defined(__CYGWIN__)
  #ifdef PROJECTNAME_BUILD_SHARED
    #define PROJECTNAME_API __declspec(dllexport)
  #else
    #define PROJECTNAME_API __declspec(dllimport)
  #endif
#elif defined(__GNUC__) || defined(__clang__)
  #ifdef PROJECTNAME_BUILD_SHARED
    #define PROJECTNAME_API __attribute__((visibility("default")))
  #else
    #define PROJECTNAME_API
  #endif
#else
  #define PROJECTNAME_API
#endif

#endif
```

Mark all public classes and free functions with the export macro:

```cpp
class PROJECTNAME_API ClassName { ... };
PROJECTNAME_API void someFunction();
```

## 5. Coordinate / Data Conventions

- Local planar coordinates: `x = North`, `y = East`, `z = Zenith`
- Heading angles: radians, `0 = North`, clockwise positive (`atan2(East, North)`)
- Cross-track error: positive when the point is **right** of the guideline, negative when left

## 6. CMake Patterns

### Root CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.14)
project(ProjectName VERSION 1.0.0 LANGUAGES CXX)

option(BUILD_SHARED_LIBS "Build as shared library" OFF)
option(BUILD_TEST_APPLICATIONS "Build test applications" ON)
option(PROJECT_BUILD_JNI "Build JNI bridge" ON)

if(ANDROID OR NOT BUILD_TEST_APPLICATIONS)
  set(BUILD_TEST_APPLICATIONS OFF)
endif()

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

add_subdirectory(src)
if(BUILD_TEST_APPLICATIONS)
  add_subdirectory(applications)
endif()
```

### src/CMakeLists.txt — Library Target

```cmake
add_library(projectname_core STATIC)

target_sources(projectname_core PRIVATE
    model/module1/ClassA.cpp
    model/module2/ClassB.cpp
)

target_include_directories(projectname_core
    PUBLIC  include
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
)

target_compile_features(projectname_core PUBLIC cxx_std_20)

if(PROJECT_BUILD_JNI AND ANDROID)
    add_subdirectory(jni)
endif()

if(BUILD_SHARED_LIBS)
    set_target_properties(projectname_core PROPERTIES
        POSITION_INDEPENDENT_CODE ON
    )
endif()
```

### Third-Party Libraries

- **Header-only libraries** (e.g. Boost, Eigen): place in `libs/` directory at project root
- **Bundled source code** (e.g. Clipper2, json, fmt): place in `src/third_party/`

## 7. Applications / Test Programs

### applications/CMakeLists.txt

```cmake
add_subdirectory(Win32)
add_subdirectory(Linux)
```

### Platform main.cpp Pattern

Each platform has a simple `main.cpp` that:
- Exercises the library API directly
- Outputs results to stdout (command-line verification)
- Uses no external testing framework

## 8. JNI Bridge Pattern (Android)

### Files

- `src/jni/` — JNI C++ source files
- `applications/Android/` — Android Studio Gradle project (separate management)

### Kotlin Side Pattern

```kotlin
package com.example.projectname

// Static native method container
object LibBridge {
    init { System.loadLibrary("native_lib") }

    @JvmStatic external fun nativeCreate(input: DoubleArray): Long
    @JvmStatic external fun nativeProcess(handle: Long, input: DoubleArray): DoubleArray
    @JvmStatic external fun nativeDestroy(handle: Long)
}

// Thin wrapper class (optional)
class Processor(private val handle: Long) {
    fun process(input: DoubleArray): DoubleArray = LibBridge.nativeProcess(handle, input)
    fun destroy() = LibBridge.nativeDestroy(handle)
    companion object {
        fun create(input: DoubleArray): Processor = Processor(LibBridge.nativeCreate(input))
    }
}
```

### C++ Side Pattern

```cpp
#include <jni.h>

extern "C" JNIEXPORT jlong JNICALL
Java_com_example_projectname_LibBridge_nativeCreate(
    JNIEnv* env, jclass clazz, jdoubleArray input) {
    auto* obj = new MyClass(/* parse input */);
    return reinterpret_cast<jlong>(obj);
}

extern "C" JNIEXPORT jdoubleArray JNICALL
Java_com_example_projectname_LibBridge_nativeProcess(
    JNIEnv* env, jclass clazz, jlong handle, jdoubleArray input) {
    auto* obj = reinterpret_cast<MyClass*>(handle);
    auto result = obj->process(/* parse input */);
    // convert result to jdoubleArray and return
}

extern "C" JNIEXPORT void JNICALL
Java_com_example_projectname_LibBridge_nativeDestroy(
    JNIEnv* env, jclass clazz, jlong handle) {
    delete reinterpret_cast<MyClass*>(handle);
}
```

### Android App CMakeLists.txt Pattern

```cmake
# In applications/Android/app/src/main/cpp/CMakeLists.txt

# Include the main library as a static dependency
add_subdirectory(../../../../../../src projectname_core)

add_library(native_lib SHARED native-lib.cpp)
target_link_libraries(native_lib
    PRIVATE
    projectname_core
    jnigraphics
    log
)
```

## 9. Comment Style

Use Chinese Doxygen-style comments for public API documentation:

```cpp
/**
 * @brief AB 直线引导线
 *
 * 根据两个基准点（A点、B点）计算直线路径，
 * 并提供横向偏差和航向角指引。
 */
class PROJECTNAME_API ABLine {
```

## 10. Build Script Convention

A `build.sh` script at project root should support:

| Command | Action |
|---------|--------|
| `./build.sh` or `./build.sh release` | Release build |
| `./build.sh debug` | Debug build |
| `./build.sh clean` | Clean build directory |
| `./build.sh rebuild` | Clean + rebuild |
| `./build.sh test` | Build and run tests |

Options:
- `-j<N>` : Parallel jobs
- `--shared` : Build shared library
- `--no-app` : Skip building test applications
