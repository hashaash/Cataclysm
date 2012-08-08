cmake_minimum_required (VERSION 2.6)
project (Cataclysm)

option (BUILD_DEBUG
	"Build with debugging support." ON)

option (BUILD_DOXYGEN
	"Create developer documentation with Doxygen." ON)

set (CURSES_NEED_NCURSES TRUE)
find_package (Curses REQUIRED)
find_package (Doxygen)

if (NOT CURSES_FOUND)
	message (FATAL "Ncurses library not found!")
endif (NOT CURSES_FOUND)

if (NOT DOXYGEN_FOUND)
	set (BUILD_DOXYGEN OFF)
	message (STATUS "Doxygen not found. Not building dev docs.")
endif (NOT DOXYGEN_FOUND)

if (BUILD_DOXYGEN)
	if (UNIX)
		configure_file (${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in
				${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)
		add_custom_target (doc
				   ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
				   WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
				   COMMENT "Generating dev docs" VERBATIM)
	endif (UNIX)
endif (BUILD_DOXYGEN)	

if (BUILD_DEBUG)
	set (CMAKE_CXX_FLAGS "-g ${CMAKE_CXX_FLAGS}")
	set (_DEBUG_ 1)
endif (BUILD_DEBUG)

set (CATACLYSM_DATA_DIR "${CMAKE_CURRENT_SOURCE_DIR}/data/")
set (CATACLYSM_SAVE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/save/")

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake ${CMAKE_CURRENT_SOURCE_DIR}/config.h)

file (GLOB CATACLYSM_SOURCES *.cpp)
include_directories (${CURSES_CURSES_H_PATH})
add_executable (cataclysm ${CATACLYSM_SOURCES})
target_link_libraries (cataclysm ${CURSES_NCURSES_LIBRARY})

message(STATUS "Your build parameters:")
message(STATUS "** Debugging       :${GLOBAL_DEBUG}")
message(STATUS "** C++ compiler    :${CMAKE_CXX_COMPILER}")
message(STATUS "** C++ flags       :${CMAKE_CXX_FLAGS}")
message(STATUS "** ncurses dir     :${CURSES_CURSES_H_PATH}")
message(STATUS "** ncurses library :${CURSES_NCURSES_LIBRARY}")
message(STATUS "** data dir        :${CATACLYSM_DATA_DIR}")
message(STATUS "** save dir        :${CATACLYSM_SAVE_DIR}")
