cmake_minimum_required(VERSION 3.13)
project(frequency_manager)

if (MSVC)
    add_compile_options(/O2 /Ob2 /std:c++17 /EHsc)
elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    add_compile_options(-O3 -std=c++17 -Wno-unused-command-line-argument -undefined dynamic_lookup)
else ()
    add_compile_options(-O3 -std=c++17)
endif ()

include_directories("src/" "../../decoder_modules/radio/src")

file(GLOB SRC "src/*.cpp")

add_library(frequency_manager SHARED ${SRC})
target_link_libraries(frequency_manager PRIVATE sdrpp_core)
set_target_properties(frequency_manager PROPERTIES PREFIX "")

# Install directives
install(TARGETS frequency_manager DESTINATION lib/sdrpp/plugins)