---
name: cpp-library-template
description: This skill should be used when the user asks to create a new C++ library project following a standardized template structure, add new modules to an existing templated project, or generate code (JNI bridge, test apps, etc.) conforming to the template's conventions. Trigger on requests like "create a new library project", "add a new module to the library", "create a JNI bridge", "create a test application", or "follow the project template".
---

# C++ Library Project Template Skill

## Purpose

Provide a standardized C++ library project template with consistent CMake build hierarchy, directory layout, coding conventions, and application/test structure. This enables rapid creation of new libraries or expansion of existing ones with predictable organization.

## When to Use

- User wants to create a brand new C++ library project from scratch
- User wants to add a new module/component to an existing library following this template
- User wants to create a JNI bridge layer for Android/Kotlin integration
- User wants to create a test/application program for the library
- User requests code that follows "the project template" or "project conventions"

## Project Structure Convention

```
ProjectName/
├── CMakeLists.txt              # Root CMake: project definition, options, subdirectories
├── build.sh                    # Build wrapper script (release/debug/clean options)
├── src/                        # Core library source
│   ├── CMakeLists.txt          # Assembles the main library target
│   ├── export.h                # API export macro definition
│   ├── ProjectNameConfig.h     # Global config structures/constants
│   ├── include/
│   │   └── projectname/        # Public headers entry point
│   │       └── ProjectName.h   # Facade header including all public types
│   └── model/                  # Business logic modules
│       ├── module1/
│       │   ├── ClassA.h
│       │   ├── ClassA.cpp
│       │   └── ...
│       ├── module2/
│       └── ...
├── applications/               # Test/demo programs
│   ├── CMakeLists.txt          # Aggregates platform subdirectories
│   ├── Win32/
│   │   ├── CMakeLists.txt
│   │   └── main.cpp
│   └── Linux/
│       ├── CMakeLists.txt
│       └── main.cpp
├── third_party/                # Bundled third-party source code
└── libs/                       # Third-party header-only libraries
```

## Conventions Reference

Detailed coding conventions, CMake patterns, and naming rules are documented in `references/coding-conventions.md`. Load this file into context when generating new source files to ensure consistency.

## Workflow Instructions

### Creating a New Library Project

1. Load `references/coding-conventions.md` for full conventions
2. Create the root `CMakeLists.txt` with:
   - `project(ProjectName VERSION 1.0.0 LANGUAGES CXX)`
   - Options: `BUILD_SHARED_LIBS`, `BUILD_TEST_APPLICATIONS`, `PROJECT_BUILD_JNI`
   - `set(CMAKE_CXX_STANDARD 17)` (or 20 as needed)
   - Output directories: `bin/` for executables, `lib/` for libraries
   - `add_subdirectory(src)` and `add_subdirectory(applications)`
3. Create `src/CMakeLists.txt` to assemble the library target
4. Create `export.h` with the platform-appropriate `DLL_EXPORT`/`DLL_IMPORT` macro
5. Create `src/include/projectname/` with a facade header
6. Create `applications/CMakeLists.txt` and platform subdirectories with simple `main.cpp` files

### Adding a New Module

1. Load conventions reference
2. Create files under `src/model/module_name/`
3. Follow the coding conventions for class naming, namespaces, and export macros
4. Update `src/CMakeLists.txt` to include new source files
5. Add public headers to `src/include/projectname/` as needed

### Creating a JNI Bridge (Android)

1. Create `src/jni/CMakeLists.txt` with JNI library target (links against `jni_lib` target)
2. Follow the generalized JNI pattern:
   - Kotlin side: `object` as static native method container, thin class(es) holding `nativeHandle: Long`
   - C++ side: `Java_com_package_Class_method` naming
   - Data transfer: `DoubleArray` for coordinate arrays
   - Memory: `nativeCreate()` / `nativeDestroy()` pair for lifecycle management
3. Android App goes in `applications/Android/` as a Gradle project with CMake native build

## Asset Notes

No bundled assets are provided by default. The template is generated from scratch based on conventions.
