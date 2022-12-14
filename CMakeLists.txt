cmake_minimum_required(VERSION 3.14)
project(xmat_lib VERSION 0.1.0)

# GoogleTest requires at least C++14
set(CMAKE_CXX_STANDARD 17)

# === google test ===
include(FetchContent)
FetchContent_Declare(
  googletest
  GIT_REPOSITORY https://github.com/google/googletest.git
  GIT_TAG release-1.12.1
)

# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)


# === add xmat ===
set(CMAKE_INCLUDE_CURRENT_DIR ON)
add_subdirectory(xmat)
add_library(xmat::xmat ALIAS xmat)
set_target_properties(xmat PROPERTIES LINKER_LANGUAGE CXX)

FIND_PACKAGE(OpenMP REQUIRED)

if (APPLE)
    set(CMAKE_CXX_COMPILER clang++)
    set(OPENMP_LIBRARIES "/opt/homebrew/opt/libomp/lib")
    set(OPENMP_INCLUDES "/opt/homebrew/opt/libomp/include")
    set(OpenMP_CXX "${CMAKE_CXX_COMPILER}")
    set(OpenMP_CXX_FLAGS "-fopenmp -lomp -Wno-unused-command-line-argument")
    set(OpenMP_CXX_LIB_NAMES "libomp" "libgomp" "libiomp5")
    set(OpenMP_libomp_LIBRARY ${OpenMP_CXX_LIB_NAMES})
    set(OpenMP_libgomp_LIBRARY ${OpenMP_CXX_LIB_NAMES})
    set(OpenMP_libiomp5_LIBRARY ${OpenMP_CXX_LIB_NAMES})
    if (OPENMP_FOUND)
        include_directories("${OPENMP_INCLUDES}")
        link_directories("${OPENMP_LIBRARIES}")
        set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    endif(OPENMP_FOUND)
else()
    # set(OpenMP_CXX_FLAGS "-fopenmp")
    if (OPENMP_FOUND)
        set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    endif(OPENMP_FOUND)
endif(APPLE)


add_subdirectory(test)