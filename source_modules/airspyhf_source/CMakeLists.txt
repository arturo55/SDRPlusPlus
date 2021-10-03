cmake_minimum_required(VERSION 3.13)
project(airspyhf_source)

if (MSVC)
    add_compile_options(/O2 /Ob2 /std:c++17 /EHsc)
elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    add_compile_options(-O3 -std=c++17 -Wno-unused-command-line-argument -undefined dynamic_lookup)
else ()
    add_compile_options(-O3 -std=c++17)
endif ()

include_directories("src/")

file(GLOB SRC "src/*.cpp")

add_library(airspyhf_source SHARED ${SRC})
target_link_libraries(airspyhf_source PRIVATE sdrpp_core)
set_target_properties(airspyhf_source PROPERTIES PREFIX "")

if (MSVC)
    # Lib path
    target_link_directories(airspyhf_source PUBLIC "C:/Program Files/PothosSDR/bin/")

    target_link_libraries(airspyhf_source PUBLIC airspyhf)
else (MSVC)
    find_package(PkgConfig)

    pkg_check_modules(LIBAIRSPYHF REQUIRED libairspyhf)

    target_include_directories(airspyhf_source PUBLIC ${LIBAIRSPYHF_INCLUDE_DIRS})
    target_link_directories(airspyhf_source PUBLIC ${LIBAIRSPYHF_LIBRARY_DIRS})
    target_link_libraries(airspyhf_source PUBLIC ${LIBAIRSPYHF_LIBRARIES})

    # Include it because for some reason pkgconfig doesn't look here?
    if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
        target_include_directories(airspyhf_source PUBLIC "/usr/local/include")
    endif()
    
endif ()

# Install directives
install(TARGETS airspyhf_source DESTINATION lib/sdrpp/plugins)