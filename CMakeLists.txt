cmake_minimum_required (VERSION 4.0.0)
project (DiscordRPC)

include(GNUInstallDirs)

option(BUILD_EXAMPLES "Build example apps" ON)

# format
file(GLOB_RECURSE ALL_SOURCE_FILES
    examples/*.cpp examples/*.h examples/*.c
    include/*.h
    src/*.cpp src/*.h src/*.c
)

# Set CLANG_FORMAT_SUFFIX if you are using custom clang-format, e.g. clang-format-5.0
find_program(CLANG_FORMAT_CMD clang-format${CLANG_FORMAT_SUFFIX})

if (CLANG_FORMAT_CMD)
    add_custom_target(
        clangformat
        COMMAND ${CLANG_FORMAT_CMD}
        -i -style=file -fallback-style=none
        ${ALL_SOURCE_FILES}
        DEPENDS
        ${ALL_SOURCE_FILES}
    )
endif(CLANG_FORMAT_CMD)

# thirdparty stuff
execute_process(
    COMMAND mkdir ${CMAKE_CURRENT_SOURCE_DIR}/thirdparty
    ERROR_QUIET
)

include(FetchContent)

FetchContent_Declare(
    rapidjson
    GIT_REPOSITORY https://github.com/Tencent/rapidjson.git
    GIT_TAG v1.1.0
)

set(RAPIDJSON_BUILD_DOC OFF CACHE BOOL "" FORCE)
set(RAPIDJSON_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(RAPIDJSON_BUILD_TESTS OFF CACHE BOOL "" FORCE)

FetchContent_MakeAvailable(rapidjson)

# add subdirs

add_subdirectory(src)

target_include_directories(discord-rpc PRIVATE
    ${rapidjson_SOURCE_DIR}/include
)

if (BUILD_EXAMPLES)
    add_subdirectory(examples/send-presence)
endif(BUILD_EXAMPLES)
