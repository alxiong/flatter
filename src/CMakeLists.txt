
#flags
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

#files

#includes
if(APPLE)
    if(CMAKE_C_COMPILER_ID MATCHES "Clang\$")
        set(OpenMP_C_FLAGS "-Xpreprocessor -fopenmp")
        set(OpenMP_C_LIB_NAMES "omp")
        set(OpenMP_omp_LIBRARY omp)
    endif()

    if(CMAKE_CXX_COMPILER_ID MATCHES "Clang\$")
        set(OpenMP_CXX_FLAGS "-Xpreprocessor -fopenmp")
        set(OpenMP_CXX_LIB_NAMES "omp")
        set(OpenMP_omp_LIBRARY omp)
    endif()
endif()
find_package(OpenMP REQUIRED)
find_package(GMP REQUIRED)
find_package(LAPACK REQUIRED)
find_package(MPFR REQUIRED)

#target
set(flatter_PUBLIC_HEADER_INSTALL_DIR include/flatter)
set(flatter_PUBLIC_HEADER_DIR
  ${CMAKE_SOURCE_DIR}/include/flatter
)

add_library(flatter SHARED "")

target_include_directories(flatter
  PRIVATE
    ${CMAKE_SOURCE_DIR}/src
    ${flatter_PUBLIC_HEADER_DIR}
)
if(APPLE)
  target_include_directories(flatter PRIVATE
    "/opt/homebrew/include"
    "/opt/homebrew/opt/libomp/include"
    "/opt/homebrew/opt/mpfr/include"
  )
endif()

add_subdirectory(math)
add_subdirectory(problems)
add_subdirectory(data)

target_sources(flatter
  PRIVATE
    flatter.cpp
    computation_context.cpp
    monitor.cpp
    profile.cpp
)

target_compile_options(flatter PRIVATE -Wall -Werror)
# Allow -Woverloaded-virtual as an exception
# as babai is overloaded in gso_interface.h and gso.h
target_compile_options(flatter PRIVATE -Wno-error=overloaded-virtual)

#external libraries
target_link_libraries(flatter
  PUBLIC
    ${GMP_LIBRARIES}
    ${LAPACK_LIBRARIES}
    ${MPFR_LIBRARIES}
    OpenMP::OpenMP_CXX
)

# installation
install(TARGETS flatter
  LIBRARY DESTINATION lib
)
install(DIRECTORY ${flatter_PUBLIC_HEADER_DIR}
        DESTINATION "include"
        FILES_MATCHING
        PATTERN "*.h"
)
