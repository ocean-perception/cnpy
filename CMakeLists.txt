CMAKE_MINIMUM_REQUIRED(VERSION 3.0 FATAL_ERROR)
if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

project(CNPY)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

option(ENABLE_STATIC "Build static (.a) library" ON)

find_package(ZLIB REQUIRED)

if (UNIX)
	include_directories(${ZLIB_INCLUDE_DIRS}) # Causes conflicts wit boost on Windows if zlib is installed in a project where parts of boost are installed as well (e.g. GDAL). Indicate full path of zlib.h in header file instead.
endif (UNIX)

add_library(cnpy SHARED "cnpy.cpp")
target_link_libraries(cnpy ${ZLIB_LIBRARIES})
if(WIN32)
	install(TARGETS "cnpy" RUNTIME DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
else()
	install(TARGETS "cnpy" LIBRARY DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
endif()

if(ENABLE_STATIC)
    add_library(cnpy-static STATIC "cnpy.cpp")
    set_target_properties(cnpy-static PROPERTIES OUTPUT_NAME "cnpy")
    install(TARGETS "cnpy-static" ARCHIVE DESTINATION lib)
endif(ENABLE_STATIC)

install(FILES "cnpy.h" DESTINATION include)
install(FILES "mat2npz" "npy2mat" "npz2mat" DESTINATION bin PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

add_executable(example1 example1.cpp)
target_link_libraries(example1 cnpy)
