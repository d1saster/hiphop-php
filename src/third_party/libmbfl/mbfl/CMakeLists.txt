
FIND_PROGRAM(AWK_EXECUTABLE NAMES gawk awk)
if(NOT EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/EastAsianWidth.txt")
	message(STATUS "Downloading EastAsianWidth.txt")
	file(DOWNLOAD "http://ftp.unicode.org/Public/UNIDATA/EastAsianWidth.txt" "${CMAKE_CURRENT_SOURCE_DIR}/EastAsianWidth.txt")
endif()

add_custom_command(OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/eaw_table.h
                   COMMAND ${AWK_EXECUTABLE} -v TABLE_NAME=mbfl_eaw_table -f mk_eaw_tbl.awk EastAsianWidth.txt > eaw_table.h
                   DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/EastAsianWidth.txt" "${CMAKE_CURRENT_SOURCE_DIR}/mk_eaw_tbl.awk")
add_custom_target(gen_eaw_table_h DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/eaw_table.h)

SET(mbfl_files mbfilter.c mbfl_string.c mbfl_language.c mbfl_encoding.c mbfl_convert.c
mbfl_ident.c mbfl_memory_device.c mbfl_allocators.c mbfl_filter_output.c
mbfilter_pass.c mbfilter_wchar.c mbfilter_8bit.c)

add_library(mbfl STATIC ${mbfl_files} ${mbfl_filters_files} ${mbfl_nls_files})
add_dependencies(mbfl gen_eaw_table_h)
