#Author: Daniel Dube (13.01.2011)

cmake_minimum_required(VERSION 2.8.0)

SET(CMAKE_BUILD_TYPE RELEASE)
#SET(CMAKE_CXX_FLAGS_DISTRIBUTION "-O3")
#SET(CMAKE_C_FLAGS_DISTRIBUTION "-O3")

#Build the library
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake_modules/")
find_package(BLAS REQUIRED)
if (NOT BLAS_FOUND)
    message(FATAL_ERROR "You have to install a package with a cblas.h header file (libblas-dev/libatlas-base-dev).")
endif(NOT BLAS_FOUND)
### set up include directories common to all subfolders
include_directories (${CMAKE_SOURCE_DIR}/src/cpp)
add_library (arac SHARED
    src/cpp/datasets/supervised.cpp
    src/cpp/datasets/sequence.cpp
    src/cpp/datasets/unsupervised.cpp
    src/cpp/structure/structure.cpp
    src/cpp/structure/component.cpp
    src/cpp/structure/modules/linear.cpp
    src/cpp/structure/modules/module.cpp
    src/cpp/structure/modules/doublegate.cpp
    src/cpp/structure/modules/switch.cpp
    src/cpp/structure/modules/gate.cpp
    src/cpp/structure/modules/partialsoftmax.cpp
    src/cpp/structure/modules/softmax.cpp
    src/cpp/structure/modules/tanh.cpp
    src/cpp/structure/modules/cosine.cpp
    src/cpp/structure/modules/lstm.cpp
    src/cpp/structure/modules/modules.cpp
    src/cpp/structure/modules/errorscaling.cpp
    src/cpp/structure/modules/multiplication.cpp
    src/cpp/structure/modules/bias.cpp
    src/cpp/structure/modules/sigmoid.cpp
    src/cpp/structure/modules/mdlstm.cpp
    src/cpp/structure/connections/blockpermutation.cpp
    src/cpp/structure/connections/linear.cpp
    src/cpp/structure/connections/connections.cpp
    src/cpp/structure/connections/permutation.cpp
    src/cpp/structure/connections/full.cpp
    src/cpp/structure/connections/convolve.cpp
    src/cpp/structure/connections/inconvolve.cpp
    src/cpp/structure/connections/identity.cpp
    src/cpp/structure/connections/connection.cpp
    src/cpp/structure/connections/outconvolve.cpp
    src/cpp/structure/parametrized.cpp
    src/cpp/structure/networks/networks.cpp
    src/cpp/structure/networks/network.cpp
    src/cpp/structure/networks/basenetwork.cpp
    src/cpp/structure/networks/mdrnns/basemdrnn.cpp
    src/cpp/structure/networks/mdrnns/mdrnn.cpp
    src/cpp/utilities/utilities.cpp
    src/cpp/arac.cpp
    src/cpp/common/buffer.cpp
    src/cpp/optimization/backprop.cpp
    src/cpp/optimization/descent/stepdescender.cpp
    src/cpp/optimization/descent/descender.cpp
)
target_link_libraries (arac
	m
    ${BLAS_LIBRARIES}
)
install (TARGETS arac LIBRARY DESTINATION lib)

# Build the tests
find_package(GTest)
if (GTEST_FOUND)
    include_directories(${GTEST_INCLUDE_DIRS})
    add_executable(test-arac
        src/cpp/tests/test_datasets.cpp
        src/cpp/tests/test_structure.cpp
        src/cpp/tests/test_main.cpp
        src/cpp/tests/test_optimzation.cpp
        src/cpp/tests/test_utilities.cpp
        )
    target_link_libraries(test-arac
        ${GTEST_LIBRARIES}
        pthread
        arac
    )
    install (TARGETS test-arac RUNTIME DESTINATION bin)
else (GTEST_FOUND)
    message("Did not find the google testing framework. Tests will not be built.")
endif (GTEST_FOUND)

#now we build the c++ interface:
add_subdirectory(src/swig)
