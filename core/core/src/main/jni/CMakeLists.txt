project(core)
cmake_minimum_required(VERSION 3.4.1)

add_subdirectory(${EXTERNAL_ROOT} external)

aux_source_directory(src SRC_LIST)
aux_source_directory(src/jni SRC_LIST)

add_library(${PROJECT_NAME} STATIC ${SRC_LIST})


set(XDL_PATH ${EXTERNAL_ROOT}/xDL/libs/${ANDROID_ABI}/libxdl.a)
#meesage("so_path: ${so_path}")
# 判断是否存在
if (NOT EXISTS ${XDL_PATH})
    message(FATAL_ERROR "xdl path not found in ${XDL_PATH}")
else()
    message("xdl path found in ${XDL_PATH}")
endif()

add_library(
        # 设置引入的静态库名称
        xdl
        # 设置引入的函数库类型为静态库
        STATIC
        # 表示引入第三方静态库
        IMPORTED)

set_target_properties(
        # 设置目标
        xdl
        # 设置属性
        PROPERTIES
        # 导入路径
        IMPORTED_LOCATION
        # ${CMAKE_SOURCE_DIR} 是本 CMakeList.txt 构建脚本的路径 , 是 CMake 工具内置的变量
        #       Android CMake 也内置了一些变量 , 如 ANDROID_ABI
        ${XDL_PATH})


target_include_directories(${PROJECT_NAME} PUBLIC include)
target_include_directories(${PROJECT_NAME} PRIVATE src)

target_link_libraries(${PROJECT_NAME} PUBLIC dobby lsplant_static log fmt-header-only)
target_link_libraries(${PROJECT_NAME} PRIVATE dex_builder_static)
target_link_libraries(${PROJECT_NAME} PUBLIC xdl)
