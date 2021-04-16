# cmakeglfw3vulkan1imgui1

# LICENSE: MIT

# Created by: Lightnet

# Credits:
 * https://github.com/ocornut/imgui

# Status:
 * Finish test build sample

# Information:
  This build is glfw 3.3.4 with vulkan 1.2.170.0 and imgui 1.82.

# Build:
  * Install CMake
  * Install build compile tool c/c++. (vs2019 tested)
  * Install vulkan

```bat
:: Create a build directory
mkdir build 
::  Build directory and get CMakeLists.txt config setup build files are in one place.
cd build && cmake .. 
:: Build the project by default Debug 64 bit
cmake --build .
```
# Vulkan:
```
Program Files\CMake\share\cmake-3.20\Modules
```

```cmake
message("Vulkan SDK in system predefine var path " $ENV{VULKAN_SDK})
This module defines the following variables::
  Vulkan_FOUND          - "True" if Vulkan was found
  Vulkan_INCLUDE_DIRS   - include directories for Vulkan
  Vulkan_LIBRARIES      - link against this library to use Vulkan
The module will also define three cache variables::
  Vulkan_INCLUDE_DIR        - the Vulkan include directory
  Vulkan_LIBRARY            - the path to the Vulkan library
  Vulkan_GLSLC_EXECUTABLE   - the path to the GLSL SPIR-V compiler

```

# Links:
 * https://vulkan.lunarg.com/sdk/home
 * https://stackoverflow.com/questions/56795645/how-can-i-add-vulkan-to-a-cross-platform-cmake-project
 * https://github.com/PacktPublishing/Learning-Vulkan/blob/master/Chapter%2003/HandShake/CMakeLists.txt
 * https://openbabel.org/dev-api/cmake_project.shtml
 