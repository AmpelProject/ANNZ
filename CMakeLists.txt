
cmake_minimum_required (VERSION 3.1.0 FATAL_ERROR)
cmake_policy(VERSION 3.1.0)

project (annz2 VERSION 2.3.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 11)

EXECUTE_PROCESS (COMMAND root-config --prefix
    OUTPUT_VARIABLE ROOT_PREFIX
    OUTPUT_STRIP_TRAILING_WHITESPACE)

set(ROOT_FIND_COMPONENTS TMVA)
include(${ROOT_PREFIX}/share/root/cmake/ROOTConfig.cmake)

# emulate old Makefile
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

configure_file(examples/scripts/commonImports.py.in examples/scripts/commonImports.py @ONLY)

include_directories(include ${ROOT_INCLUDE_DIRS})
add_library(ANNZ SHARED
  src/ANNZ.cpp
  src/BaseClass.cpp
  src/CatFormat.cpp
  src/OptMaps.cpp
  src/OutMngr.cpp
  src/Utils.cpp
  src/VarMaps.cpp
)

add_executable(myANNZ src/myANNZ.cpp)
target_link_libraries(myANNZ ANNZ ${ROOT_LIBRARIES})

add_library(Wrapper SHARED src/myANNZ.cpp src/Wrapper.cpp)
target_link_libraries(Wrapper ANNZ ${ROOT_LIBRARIES})
set_target_properties(Wrapper PROPERTIES LINKER_LANGUAGE C OUTPUT_NAME "Wrapper" PREFIX "" SUFFIX ".so")

