cmake_minimum_required(VERSION 3.22)
project(clion-jdk)

set(CMAKE_CXX_STANDARD 17)

set(CMAKE_CXX_FLAGS "-fpermissive -Wno-format-zero-length -Wconversion")

set(build_path "./build")

MACRO(expand_glob result pattern)
    file(GLOB elements RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ${pattern})

    set(dir_list "")
    FOREACH (e ${elements})
        IF (IS_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/${e})
            list(APPEND dir_list ${e})
        ENDIF ()
    ENDFOREACH ()

    set(${result} ${dir_list})
ENDMACRO()

MACRO(include_glob pattern)
    expand_glob(dir_list "${pattern}")
    include_directories(${dir_list})
ENDMACRO()

function(include_under curdir)
    include_directories(${curdir})

    expand_glob(dir_list ${curdir}/*)
    include_directories(${dir_list})
endfunction()

include_directories(${build_path}/linux-x64-debug/jdk/include/)
include_directories(${build_path}/linux-x64-debug/hotspot/variant-server/gensrc/)

include_directories(${build_path}/linux-x64-debug/support/modules_include/java.base)
include_directories(${build_path}/linux-x64-debug/support/modules_include/java.base/linux/)

include_under(${build_path}/linux-x86_64-server-fastdebug/support/headers/)

include_directories(src/hotspot/share/)
include_directories(src/hotspot/share/include)
include_directories(src/hotspot/share/precompiled/)

include_directories(src/hotspot/cpu/x86/)
include_directories(src/hotspot/os/linux/)
include_directories(src/hotspot/os/posix/)
include_directories(src/hotspot/os/posix/include)
include_directories(src/hotspot/os_cpu/linux_x86/)

include_glob(src/*/share/native/*)
include_glob(src/*/unix/native/*)
include_glob(src/*/linux/native/*)

include_glob(src/*/share/native/*/*)
include_glob(src/*/unix/native/*/*)
include_glob(src/*)

add_definitions(-D_XOPEN_SOURCE)
add_definitions(-D_GNU_SOURCE)
add_definitions(-DLINUX)
add_definitions(-DSUPPORTS_CLOCK_MONOTONIC)

add_definitions(-DASSERT)
add_definitions(-DDONT_USE_PRECOMPILED_HEADER)
add_definitions(-DAMD64)
add_definitions(-DHOTSPOT_LIB_ARCH="amd64")
add_definitions(-DARCHPROPNAME="amd64")
add_definitions(-DTARGET_ARCH_x86)
add_definitions(-DINCLUDE_SUFFIX_OS=_linux)
add_definitions(-DINCLUDE_SUFFIX_CPU=_x86)
add_definitions(-DINCLUDE_SUFFIX_COMPILER=_gcc)
add_definitions(-DTARGET_COMPILER_gcc)

add_definitions(-DCOMPILER1)
add_definitions(-DCOMPILER2)
add_definitions(-DINCLUDE_AOT)

add_definitions(-DVERSION_MAJOR=0)
add_definitions(-DVERSION_MINOR=0)
add_definitions(-DVERSION_SECURITY=0)
add_definitions(-DVERSION_PATCH=0)
add_definitions(-DVERSION_FEATURE=0)
add_definitions(-DVERSION_INTERIM=0)
add_definitions(-DVERSION_UPDATE=0)

add_definitions(-DVERSION_BUILD=0)
add_definitions(-DVERSION_STRING="0-internal+0-adhoc.openjdk.vm")
add_definitions(-DHOTSPOT_VERSION_STRING="0-internal+0-adhoc.openjdk.vm")

add_definitions(-DDEBUG_LEVEL="fastdebug")
add_definitions(-DHOTSPOT_VM_DISTRO="OpenJDK")
add_definitions(-DHOTSPOT_BUILD_USER="openjdk")

file(GLOB_RECURSE SOURCE_FILES
        src/*/*.c src/*/*.cpp
        src/*/*.h src/*/*.hpp
        ${build_path}/linux-x86_64-server-fastdebug/*/*.cpp
)

list(FILTER SOURCE_FILES EXCLUDE REGEX ".*java.desktop.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*jdk.jdi.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*share/shark.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*hsdis.*")

# cpu
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*aarch64.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*arm.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*ppc.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*riscv.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*s390.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*x86_32.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*zero.*")

# os
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*aix.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*bsd.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*macosx.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*solaris.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*windows.*")
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*libadt/dict.*")

add_executable(java ${SOURCE_FILES})