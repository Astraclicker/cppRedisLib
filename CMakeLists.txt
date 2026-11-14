cmake_minimum_required(VERSION 3.15)

project(cppRedisLib LANGUAGES C CXX)

# ---------------------------------------------------------------------------
# cppRedisLib bundles hiredis + redis-plus-plus into one dependency.
#
#   add_subdirectory(cppRedisLib)
#   target_link_libraries(my_target PRIVATE cppRedisLib)
#
# Linking cppRedisLib is enough: the include directories and the libraries of
# hiredis and redis-plus-plus are propagated transitively.
# ---------------------------------------------------------------------------
add_subdirectory(hiredis)
add_subdirectory(redis-plus-plus)

add_library(cppRedisLib INTERFACE)
add_library(cppRedisLib::cppRedisLib ALIAS cppRedisLib)

target_link_libraries(cppRedisLib INTERFACE
    hiredis::hiredis
    redis++::redis++)

# Make <hiredis/...> and <sw/redis++/...> reachable from consumers; both are
# already PUBLIC usage requirements of the imported targets above, this is
# just an explicit safety net for include paths.
target_include_directories(cppRedisLib INTERFACE
    # include root that contains the "hiredis" directory
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
    # include root that contains the "sw/redis++" directory
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/redis-plus-plus/src>")
