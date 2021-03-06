cmake_minimum_required(VERSION 2.8.3)

get_filename_component(REAL_PROJECT_ROOT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../.." REALPATH)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR} ${PROJECT_SOURCE_DIR} ${REAL_PROJECT_ROOT_DIR})

if(PROJECT_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    find_package(VarnishAPI)
    find_package(Hiredis REQUIRED)

    add_definitions(-DREDIS_SHARED_DRIVER=1)
    get_filename_component(REAL_PROJECT_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../src" REALPATH)
    declare_vmod(
        INSTALL
        NAME keystore_redis
        VCC ${CMAKE_CURRENT_SOURCE_DIR}/src/vmod_keystore_redis.vcc
        SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/vmod_keystore_redis.c
        ADDITIONNAL_LIBRARIES ${HIREDIS_LIBRARIES} "${VARNISHAPI_VMODDIR}/libvmod_keystore.so"
        ADDITIONNAL_INCLUDE_DIRECTORIES ${HIREDIS_INCLUDE_DIRS} ${REAL_PROJECT_SOURCE_DIR}
    )
else(PROJECT_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    find_package(Hiredis QUIET)
    if(HIREDIS_FOUND)
        declare_driver(
            NAME "redis"
            SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/vmod_keystore_redis.c
            ADDITIONNAL_LIBRARIES ${HIREDIS_LIBRARIES}
            ADDITIONNAL_INCLUDE_DIRECTORIES ${HIREDIS_INCLUDE_DIRS}
        )
    endif(HIREDIS_FOUND)
endif(PROJECT_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
