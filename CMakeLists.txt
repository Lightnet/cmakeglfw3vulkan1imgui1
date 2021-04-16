# https://www.glfw.org/docs/latest/build_guide.html
cmake_minimum_required(VERSION 3.20) # FetchContent is available in 3.11+

project(boilerplate VERSION 0.1.0)

set(ENTRYPOINT 
	#hellworld.cpp
	#HelloTriangleApplication.cpp
	glfw3vulkan1imgui.cpp
)

# https://stackoverflow.com/questions/36089291/why-cmake-library-output-directory-doesnt-work
# https://bytefreaks.net/tag/cmake_archive_output_directory
# user must config manual
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}) # /lib
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}) # /lib
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}) # /bin

message("CMAKE_BINARY_DIR  " ${CMAKE_BINARY_DIR})

message("CMAKE_ARCHIVE_OUTPUT_DIRECTORY: ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}") # workspace/build/Debug
message("CMAKE_LIBRARY_OUTPUT_DIRECTORY: ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}") # workspace/build/Debug
message("CMAKE_RUNTIME_OUTPUT_DIRECTORY: ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}") # workspace/build/Debug

# https://cmake.org/cmake/help/latest/module/FetchContent.html
# dev settings for fast compile time
#set(FETCHCONTENT_FULLY_DISCONNECTED ON) # Stop Download and Update
set(FETCHCONTENT_UPDATES_DISCONNECTED ON) # Stop update

#set(FETCHCONTENT_SOURCE_DIR_GLFW ON) # Upper case prefix name # Stop Download and Update
#set(FETCHCONTENT_UPDATES_DISCONNECTED_GLFW ON) # Upper case prefix name # Stop update

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set(CPP_INCLUDE_DIRS "")
set(CPP_SOURCES_FILES "")
set(CPP_HEADERS_FILES "")

# set the module path
#set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake") #nope incase this project become module downloaded from git it get error
#list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake") #pass
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/modules)

#message("CMAKE_MODULE_PATH: " ${CMAKE_MODULE_PATH})
#message("CMAKE_SOURCE_DIR: " ${CMAKE_SOURCE_DIR})

# https://gitlab.kitware.com/cmake/community/-/wikis/doc/tutorials/How-To-Find-Libraries
# it can be found in cmake folder program files
find_package(Vulkan) #
#message("Vulkan_FOUND:" ${Vulkan_FOUND})
#message("Vulkan_INCLUDE_DIRS:" ${Vulkan_INCLUDE_DIRS})
#message("Vulkan_LIBRARIES:" ${Vulkan_LIBRARIES})
#message("Vulkan_LIBRARY:" ${Vulkan_LIBRARY})

# note vulkan has to be install normal since script default
if (Vulkan_FOUND)
  list(APPEND CPP_INCLUDE_DIRS ${Vulkan_INCLUDE_DIRS})
  set(VULKAN_PATH ${Vulkan_INCLUDE_DIRS})
  STRING(REGEX REPLACE "/Include" "" VULKAN_PATH ${VULKAN_PATH})
  # Include Vulkan header files from Vulkan SDK
  #message("VULKAN_PATH " ${VULKAN_PATH})
endif()

if (NOT Vulkan_FOUND) # If there's none
  message("NOT FOUND VULKAN DIR")
  return()
endif()
#set(VULKAN_LIB_LIST "vulkan-1")

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  include_directories(${Vulkan_INCLUDE_DIRS})
  # Link directory for vulkan-1
  link_directories(${VULKAN_PATH}/Bin;${VULKAN_PATH}/Lib;)
endif()

if (NOT glfw_FOUND)
  include(FetchContent)
  FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw.git
    GIT_TAG 3.3.4
  )
  FetchContent_GetProperties(glfw)
  if (NOT glfw_POPULATED) # Have we downloaded glfw yet?
    set(FETCHCONTENT_QUIET NO)
    FetchContent_Populate(glfw)

    #set(BUILD_SHARED_LIBS ON CACHE BOOL "" FORCE)
    set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE) # don't build the supplied examples
    set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE) # don't build the tests
    set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE) # don't build the docs
    set(GLFW_INSTALL OFF CACHE INTERNAL "" FORCE)
    include_directories(${glfw_SOURCE_DIR}/include)
    list(APPEND CPP_INCLUDE_DIRS ${glfw_SOURCE_DIR}/include)

    message("glfw_SOURCE_DIR " ${glfw_SOURCE_DIR})
    message("glfw_BINARY_DIR " ${glfw_BINARY_DIR})
    add_subdirectory(${glfw_SOURCE_DIR} ${glfw_BINARY_DIR}) # compile build output

    #link_directories(${glfw_BINARY_DIR}/src/Debug) # dll look lib    
  endif()
endif()

if (NOT imgui_FOUND)
  include(FetchContent)
  FetchContent_Declare(
    imgui
    GIT_REPOSITORY https://github.com/ocornut/imgui.git
    GIT_TAG v1.82
  )
  FetchContent_GetProperties(imgui)
  if (NOT cimguigit_POPULATED) # Have we downloaded imgui yet?
    set(FETCHCONTENT_QUIET NO)
    FetchContent_Populate(imgui)
    #message("imgui_SOURCE_DIR " ${imgui_SOURCE_DIR})
    include_directories(${imgui_SOURCE_DIR})
    include_directories(${imgui_SOURCE_DIR}/backends)

    list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imgui.cpp)
    list(APPEND CPP_SOURCES_FILES ${imgui_SOURCE_DIR}/imgui_demo.cpp)
    list(APPEND CPP_SOURCES_FILES ${imgui_SOURCE_DIR}/imgui_draw.cpp)
    list(APPEND CPP_SOURCES_FILES ${imgui_SOURCE_DIR}/imgui_tables.cpp)
    list(APPEND CPP_SOURCES_FILES ${imgui_SOURCE_DIR}/imgui_widgets.cpp)
    list(APPEND CPP_SOURCES_FILES ${imgui_SOURCE_DIR}/backends/imgui_impl_glfw.cpp)
    list(APPEND CPP_SOURCES_FILES ${imgui_SOURCE_DIR}/backends/imgui_impl_vulkan.cpp)

    list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imgui.h)
    list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imconfig.h)
    list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imgui_internal.h)
    #list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imstb_rectpack.h)
    #list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imstb_textedit.h)
    #list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/imstb_truetype.h)
    list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/backends/imgui_impl_glfw.h)
    list(APPEND CPP_HEADERS_FILES ${imgui_SOURCE_DIR}/backends/imgui_impl_vulkan.h)

    list(APPEND CPP_INCLUDE_DIRS ${imgui_SOURCE_DIR})
    list(APPEND CPP_INCLUDE_DIRS ${imgui_SOURCE_DIR}/backends)
  endif()
endif()

#find_library(glfw3_LIB NAMES glfw3 HINTS ${glfw_BINARY_DIR}/src/Debug) #lib search
link_directories(${CMAKE_BINARY_DIR}/Debug)

# Add source to this project's executable.
add_executable(${PROJECT_NAME} 
  ${ENTRYPOINT}
  ${CPP_HEADERS_FILES}
  ${CPP_SOURCES_FILES}
)

target_link_libraries(${PROJECT_NAME} 
  PRIVATE
  glfw3
  vulkan-1
  #${glfw3_LIB}
) # dll / lib