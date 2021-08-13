cmake_minimum_required(VERSION 3.19)

set(PROJ_NAME cppUtils)
project(${PROJ_NAME})

# Compiler Definitions
set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/install)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

set(CMAKE_DEBUG_POSTFIX "d")
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Compile Options
option(CPPUTILS_BUILD_TEST                     "Build unit tests" ${CPPUTILS_BUILD_TEST})
option(CPPUTILS_BUILD_SAMPLES                  "Build samples" ${CPPUTILS_BUILD_SAMPLES})

include_directories(include)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)


if(CPPUTILS_BUILD_SAMPLES)
	add_subdirectory(samples)
endif()

if(CPPUTILS_BUILD_TEST)
	set(PATH_GMOCK ${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/googletest/googlemock CACHE PATH "This directory contains Google Mock MAIN Path")
	add_definitions(-DGTEST_CREATE_SHARED_LIBRARY=1)
	set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
	add_subdirectory(${PATH_GMOCK})
	set_target_properties(gmock PROPERTIES FOLDER "3rdparty")
	set_target_properties(gmock_main PROPERTIES FOLDER "3rdparty")
	set_target_properties(gtest PROPERTIES FOLDER "3rdparty")
	set_target_properties(gtest_main PROPERTIES FOLDER "3rdparty")
	enable_testing()
	set(TEST_DATA_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/test_data CACHE PATH "Folder with the testing data")
	add_definitions(-DDATA_FOLDER="${TEST_DATA_FOLDER}")
	add_subdirectory(tests)
endif()

get_property(LOCAL_POST_BUILD_DEPENDENCIES GLOBAL PROPERTY POST_BUILD_DEPENDENCIES)

ADD_CUSTOM_TARGET(ALL_BUILD_WITH_POST_EVENT ALL
  COMMAND ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/$<CONFIG>/plugin_config_params_parser
  COMMENT "All build with post event"
)

#export(EXPORT ${PROJ_NAME} NAMESPACE ${PROJ_NAME}:: FILE CppUtilsTargets.cmake)
#install(EXPORT ${PROJ_NAME} NAMESPACE ${PROJ_NAME}:: DESTINATION cmake FILE CppUtilsTargets.cmake )


