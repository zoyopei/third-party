cmake_minimum_required(VERSION 3.9)

project(thrid-party LANGUAGES C CXX)

option(BUILD_RAPIDJSON "build rapidjson" ON)
option(BUILD_CURL "build rapidjson" ON)
option(BUILD_BOOST "build boost" OFF)

set(INSTALL_PREFIX "${CMAKE_BINARY_DIR}" CACHE PATH "install prefix")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC" CACHE STRING "cmake c flags")

include(ExternalProject)

if (BUILD_RAPIDJSON)
    ExternalProject_Add(
        rapidjson
        PREFIX ${PREFIX}
        GIT_REPOSITORY "https://github.com/Tencent/rapidjson.git"
        #GIT_TAG "origin/master"
        GIT_TAG "ab1842a2dae061284c0a62dca1cc6d5e7e37e346"
        CMAKE_ARGS
            -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
            -DBUILD_TESTING:BOOL=OFF
            -DRAPIDJSON_BUILD_CXX17:BOOL=ON
            -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
            -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
    )
endif()

if (BUILD_CURL)
    ExternalProject_Add(
        curl 
        PREFIX ${PREFIX}
        URL "https://curl.se/download/curl-8.9.1.tar.gz"
        CMAKE_ARGS
            -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
            -DBUILD_STATIC_CURL:BOOL=ON
            -DBUILD_STATIC_LIBS:BOOL=ON
            -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
            -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
    )
endif()

if (BUILD_BOOST)
    ExternalProject_Add(
        boost
        #URL "https://archives.boost.io/release/1.85.0/source/boost_1_85_0.tar.gz"
        URL "ftp://ftp.4pd.io/pub/pico/blink/boost_1_85_0.tar.gz"
        BUILD_IN_SOURCE true
        CONFIGURE_COMMAND ./bootstrap.sh
        BUILD_COMMAND  ./b2
            --prefix=${INSTALL_PREFIX}
            --without-python
            link=static
            runtime-link=static
            install
            -j8
      INSTALL_COMMAND ""
    )
endif()

ExternalProject_Add(
    readwritequeue 
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/cameron314/readerwriterqueue.git"
    GIT_TAG "v1.0.6"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)

ExternalProject_Add(
    concurrentqueue 
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/cameron314/concurrentqueue.git"
    GIT_TAG "v1.0.4"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)

ExternalProject_Add(
    libevent 
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/libevent/libevent.git"
    GIT_TAG "release-2.1.12-stable"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)

ExternalProject_Add(
    gflags
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/gflags/gflags.git"
    GIT_TAG "v2.2.2"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)

ExternalProject_Add(
    gtest
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/google/googletest.git"
    GIT_TAG "v1.15.2"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)

ExternalProject_Add(
    gperftools
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/gperftools/gperftools.git"
    GIT_TAG "gperftools-2.15"
    BUILD_IN_SOURCE true
    CONFIGURE_COMMAND
        #rm m4/libtool.m4 && bash autogen.sh
        test -e m4/libtool.m4 || ./autogen.sh
        COMMAND ./configure --prefix=${INSTALL_PREFIX}
            "CFLAGS=${CMAKE_C_FLAGS}"
)

ExternalProject_Add(
    libuv
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/libuv/libuv.git"
    GIT_TAG "v1.48.0"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)

ExternalProject_Add(
    jemalloc
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/jemalloc/jemalloc.git"
    GIT_TAG "5.3.0"
    BUILD_IN_SOURCE true
    CONFIGURE_COMMAND
        ./autogen.sh
        COMMAND ./configure --prefix=${INSTALL_PREFIX}
            "CFLAGS=${CMAKE_C_FLAGS}"
)

ExternalProject_Add(
    absl
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/abseil/abseil-cpp.git"
    GIT_TAG "20240722.0"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DABSL_BUILD_TEST_HELPERS:BOOL=ON
        -DABSL_USE_EXTERNAL_GOOGLETEST:BOOL=ON
        -DCMAKE_PREFIX_PATH:PATH=${INSTALL_PREFIX}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
    DEPENDS gtest
)

ExternalProject_Add(
    protobuf
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/protocolbuffers/protobuf.git"
    GIT_TAG "v25.4"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -Dprotobuf_ABSL_PROVIDER:STRING=package
        -DCMAKE_PREFIX_PATH:PATH=${INSTALL_PREFIX}
        -Dprotobuf_BUILD_TESTS:BOOL=OFF
    DEPENDS gtest absl
)

ExternalProject_Add(
    leveldb
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/google/leveldb.git"
    GIT_TAG "1.23"
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)


ExternalProject_Add(
    brpc
    PREFIX ${PREFIX}
    GIT_REPOSITORY "https://github.com/apache/brpc.git"
    GIT_TAG "1.9.0"
    PATCH_COMMAND git checkout . && git apply ${CMAKE_CURRENT_LIST_DIR}/brpc-utf8_range.patch
    CMAKE_ARGS
        -DCMAKE_C_FLAGS:STRING=${CMAKE_C_FLAGS}
        -DCMAKE_PREFIX_PATH:PATH=${INSTALL_PREFIX}
        -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_PREFIX}
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        #-Dabsl_DIR:PATH=${INSTALL_PREFIX}/lib/cmake/absl
    DEPENDS gflags rapidjson protobuf absl leveldb
)
