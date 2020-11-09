cmake_minimum_required(VERSION 2.6)
project(hdiffpatch)


set(HDIFF_VERSION_TAG "" CACHE STRING "HDIFF_VERSION_TAG override, default is HDIFF13")

if (HDIFF_VERSION_TAG)
	add_definitions(-DHDIFF_VERSION_TAG="\"${HDIFF_VERSION_TAG}\"")
endif()

set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
set(THREADS_PREFER_PTHREAD_FLAG TRUE)
find_package(Threads REQUIRED)

include_directories(libHDiffPatch)

add_library(hdiffpatch STATIC
	libHDiffPatch/HDiff/diff.cpp
	libHDiffPatch/HDiff/private_diff/bytes_rle.cpp
	libHDiffPatch/HDiff/private_diff/compress_detect.cpp
	libHDiffPatch/HDiff/private_diff/libdivsufsort/divsufsort.c
	libHDiffPatch/HDiff/private_diff/libdivsufsort/divsufsort.c.inc.h
	libHDiffPatch/HDiff/private_diff/libdivsufsort/divsufsort64.c
	libHDiffPatch/HDiff/private_diff/libdivsufsort/sssort.c.inc.h
	libHDiffPatch/HDiff/private_diff/libdivsufsort/trsort.c.inc.h
	libHDiffPatch/HDiff/private_diff/libdivsufsort/utils.c.inc.h
	libHDiffPatch/HDiff/private_diff/limit_mem_diff/adler_roll.c
	libHDiffPatch/HDiff/private_diff/limit_mem_diff/digest_matcher.cpp
	libHDiffPatch/HDiff/private_diff/limit_mem_diff/stream_serialize.cpp
	libHDiffPatch/HDiff/private_diff/suffix_string.cpp
	libHDiffPatch/HPatch/patch.c

	libParallel/parallel_channel.cpp
	libParallel/parallel_import.cpp
	compress_parallel.cpp
)

add_definitions(-D_FILE_OFFSET_BITS=64)
add_definitions(-D_IS_NEED_ORIGINAL=1)
add_definitions(-D_IS_NEED_ALL_CompressPlugin=0)
add_definitions(-D_IS_NEED_DEFAULT_CompressPlugin=0)
add_definitions(-D_CompressPlugin_zlib)
add_definitions(-D_CompressPlugin_bz2)
add_definitions(-D_IS_NEED_ALL_ChecksumPlugin=0)
add_definitions(-D_IS_NEED_DEFAULT_ChecksumPlugin=0)
add_definitions(-D_IS_NEED_DIR_DIFF_PATCH=0)
add_definitions(-D_IS_USED_MULTITHREAD=1)
add_definitions(-D_IS_USED_PTHREAD=1)

target_link_libraries(
	hdiffpatch z bz2 Threads::Threads
)

install(
	FILES
		libHDiffPatch/HDiff/diff.h
		libHDiffPatch/HDiff/diff_types.h
	DESTINATION
		include/hdiffpatch/HDiff
)
install(
	FILES
		libHDiffPatch/HPatch/checksum_plugin.h
		libHDiffPatch/HPatch/patch.h
		libHDiffPatch/HPatch/patch_private.h
		libHDiffPatch/HPatch/patch_types.h
	DESTINATION
		include/hdiffpatch/HPatch
)
install(TARGETS hdiffpatch
    ARCHIVE DESTINATION lib
)
