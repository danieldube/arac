find_package(PythonInterp)
find_package(PythonLibs)

# cmake 2.4 (at least on Ubuntu 8.04 (hardy)) don't define PYTHONLIBS_FOUND
if(NOT PYTHONLIBS_FOUND AND PYTHON_INCLUDE_PATH)
    set(PYTHONLIBS_FOUND ON)
endif(NOT PYTHONLIBS_FOUND AND PYTHON_INCLUDE_PATH)
    
execute_process(COMMAND ${PYTHON_EXECUTABLE} --version
          ERROR_VARIABLE PYTHON_VERSION_FULL
          OUTPUT_STRIP_TRAILING_WHITESPACE)

string(REGEX MATCH "[0-9].[0-9]" PYTHON_VERSION_MAJOR_MINOR "${PYTHON_VERSION_FULL}")
if(UNIX)
    set(PYTHON_PLUGIN_INSTALL_PATH lib/python${PYTHON_VERSION_MAJOR_MINOR}/site-packages/arac)
    set(PYTHON_PACKAGES_PATH lib/python${PYTHON_VERSION_MAJOR_MINOR}/site-packages)
endif()
if(WIN32)
    get_filename_component(PYTHON_PATH "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Python\\PythonCore\\${PYTHON_VERSION_MAJOR_MINOR}\\InstallPath]" ABSOLUTE CACHE)
    set(PYTHON_PLUGIN_INSTALL_PATH "${PYTHON_PATH}/Lib/site-packages/arac")
    set(PYTHON_PACKAGES_PATH "${PYTHON_PATH}/Lib/site-packages")
endif()

message("Python path: "${PYTHON_PLUGIN_INSTALL_PATH})


FIND_PACKAGE(SWIG REQUIRED)
INCLUDE(${SWIG_USE_FILE})

FIND_PACKAGE(PythonLibs)
INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_PATH})

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

SET(CMAKE_SWIG_FLAGS "")
SET_SOURCE_FILES_PROPERTIES(cppbridge.i PROPERTIES CPLUSPLUS ON)
SET_SOURCE_FILES_PROPERTIES(cppbridge.i PROPERTIES SWIG_FLAGS "-includeall")
SWIG_ADD_MODULE(cppbridge python cppbridge.i)
SWIG_LINK_LIBRARIES(cppbridge ${PYTHON_LIBRARIES} arac)

get_target_property(LOC_CPPBRIDGE ${SWIG_MODULE_cppbridge_REAL_NAME} LOCATION)

set(python_files 
    ../python/arac/__init__.py
    ../python/arac/utilities.py
    ../python/arac/pybrainbridge.py
    ${CMAKE_CURRENT_BINARY_DIR}/cppbridge.py
    ${LOC_CPPBRIDGE}
    )

if(WIN32)
    install(FILES ${pyopencv_files} DESTINATION
        "Python${PYTHON_VERSION_MAJOR_MINOR}/Lib/site-packages/opencv"
        COMPONENT main)
else()
    install(FILES ${python_files} DESTINATION ${PYTHON_PLUGIN_INSTALL_PATH} COMPONENT main)
endif()

message(${CMAKE_INSTALL_PREFIX})


