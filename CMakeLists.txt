cmake_minimum_required(VERSION 3.5.1 FATAL_ERROR)

find_program(LSB_RELEASE_EXEC lsb_release)
execute_process(COMMAND ${LSB_RELEASE_EXEC} -is OUTPUT_VARIABLE LSB_RELEASE_ID_SHORT OUTPUT_STRIP_TRAILING_WHITESPACE)

execute_process(COMMAND ${LSB_RELEASE_EXEC} -cs OUTPUT_VARIABLE RELEASE_CODENAME OUTPUT_STRIP_TRAILING_WHITESPACE)

message("Building for ${LSB_RELEASE_ID_SHORT}/${RELEASE_CODENAME}")

project(word2vec)

LIST(APPEND CMAKE_MODULE_PATH "$ENV{HOME}/cmake/modules")
# See https://blog.kitware.com/cmake-and-the-default-build-type/
find_package(BuildType)
find_package(Threads)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR}/bin)

file(GLOB C_FILES src/*.c)

set_source_files_properties(${C_FILES} PROPERTIES COMPILE_FLAGS "-march=native -Wall -ggdb -fno-omit-frame-pointer -funroll-loops -Wno-unused-result")

foreach (source ${C_FILES})
	get_filename_component(barename ${source} NAME_WE)
	add_executable(${barename} ${source})
	install(TARGETS ${barename} DESTINATION ${CMAKE_INSTALL_PREFIX})
	target_link_libraries(${barename} ${CMAKE_THREAD_LIBS_INIT} m)
endforeach(source)

