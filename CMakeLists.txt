cmake_minimum_required(VERSION 3.7)
project(Filters)

include(filters.cmake)

add_definitions(-fpic)

SET(FF_SRC_H26X
        ${CMAKE_CURRENT_SOURCE_DIR}/ff_dec-h26x.c
        ${CMAKE_CURRENT_SOURCE_DIR}/ff_common.c
        ${CMAKE_CURRENT_SOURCE_DIR}/reframe_h263.c
)

SET(FF_LIB_H26X
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/libavcodec.a
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/libavutil.a
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/libavformat.a
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/libavfilter.a
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/libswscale.a
)

SET(FF_INCLUDE_H26X
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

add_filter(ffmpeg-h26x
        "${FF_SRC_H26X}"
        "${FF_LIB_H26X}"
        []
        GPAC_HAS_FFMPEG
        "${FF_INCLUDE_H26X}"
        ""
        "1")
