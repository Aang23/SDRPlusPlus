cmake_minimum_required(VERSION 3.13)
project(sdrplay_source)

if (MSVC)
    set(CMAKE_CXX_FLAGS "-O2 /std:c++17 /EHsc")
else()
    set(CMAKE_CXX_FLAGS "-O3 -std=c++17 -fpermissive")
endif (MSVC)

include_directories("src/")

file(GLOB SRC "src/*.cpp")

add_library(sdrplay_source SHARED ${SRC})
target_link_libraries(sdrplay_source PRIVATE sdrpp_core)
set_target_properties(sdrplay_source PROPERTIES PREFIX "")

if (MSVC)
    # Lib path
    target_link_directories(sdrpp_core PUBLIC "C:/Program Files/SDRplay/API/x64")
    target_include_directories(sdrplay_source PUBLIC "C:/Program Files/SDRplay/API/inc")

    target_link_libraries(sdrplay_source PUBLIC sdrplay_api)
else (MSVC)
    find_package(PkgConfig)

    pkg_check_modules(LIBSDRPLAY REQUIRED libsdrplay)

    target_include_directories(sdrplay_source PUBLIC ${LIBSDRPLAY_INCLUDE_DIRS})
    target_link_directories(sdrplay_source PUBLIC ${LIBSDRPLAY_LIBRARY_DIRS})
    target_link_libraries(sdrplay_source PUBLIC ${LIBSDRPLAY_LIBRARIES})
endif (MSVC)

# Install directives
install(TARGETS sdrplay_source DESTINATION lib/sdrpp/plugins)