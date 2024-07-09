#flags
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

#files

#target
add_executable(flatter_bin flatter.cpp)
target_compile_options(flatter_bin PRIVATE -Wall -Werror)
target_link_libraries(flatter_bin flatter)
set_target_properties(flatter_bin
    PROPERTIES OUTPUT_NAME flatter
)

#includes
if(APPLE)
  target_include_directories(flatter_bin PRIVATE
    "/opt/homebrew/include"
    "/opt/homebrew/opt/libomp/include"
    "/opt/homebrew/opt/mpfr/include"
  )
endif()

#external libraries

# installation
install(TARGETS flatter_bin
    DESTINATION bin
)

#testing
