# Project root is two levels up from ns-3 source directory
set(PROJECT_ROOT "${CMAKE_SOURCE_DIR}/../..")
set(E2SIM_INCLUDE_DIR "${PROJECT_ROOT}/install/include/e2sim")
set(E2SIM_LIB_DIR "${PROJECT_ROOT}/install/lib")

# IMPORTANT: e2sim include paths must be set globally via CMAKE_CXX_FLAGS at configure time
# because contrib module CMakeLists.txt is processed too late for other modules (like lte)
# To build: ./ns3 configure -- -DCMAKE_CXX_FLAGS="-I${CMAKE_SOURCE_DIR}/../../install/include/e2sim"
# Alternatively, set via environment: export CMAKE_CXX_FLAGS="-I<project_root>/install/include/e2sim"

# These are processed within this module's scope
include_directories(${CMAKE_BINARY_DIR}/include/ns3)
link_directories(${E2SIM_LIB_DIR})
link_libraries(e2sim)

# Define the source files
set(source_files
    model/oran-interface.cc
    model/asn1c-types.cc
    model/function-description.cc
    model/kpm-indication.cc
    model/kpm-function-description.cc
    model/ric-control-message.cc
    model/ric-control-function-description.cc
    helper/oran-interface-helper.cc
    helper/indication-message-helper.cc
    helper/lte-indication-message-helper.cc
    helper/mmwave-indication-message-helper.cc
)

# Define the header files
set(header_files
    model/oran-interface.h
    model/asn1c-types.h
    model/function-description.h
    model/kpm-indication.h
    model/kpm-function-description.h
    model/ric-control-message.h
    model/ric-control-function-description.h
    helper/oran-interface-helper.h
    helper/indication-message-helper.h
    helper/lte-indication-message-helper.h
    helper/mmwave-indication-message-helper.h
)

# Define the test sources (empty if no tests are needed)
set(test_sources "")  # Initialize test_sources to prevent CMake warnings

# Create the oran-interface library using build_lib
build_lib(
  LIBNAME oran-interface
  SOURCE_FILES ${source_files}
  HEADER_FILES ${header_files}
  LIBRARIES_TO_LINK
    ${libcore}
  e2sim
  TEST_SOURCES ${test_sources}
)

# Alternatively, create the oran-interface library manually
# IMPORTANT: Use PUBLIC linkage for e2sim so that executables linking against
# oran-interface will also link against e2sim (required for static libraries)
add_library(oran-interface STATIC ${source_files} ${header_files})
target_include_directories(oran-interface PUBLIC ${E2SIM_INCLUDE_DIR})
target_link_directories(oran-interface PUBLIC ${E2SIM_LIB_DIR})
target_link_libraries(oran-interface PUBLIC e2sim)

# Specify the install directory for ns3 includes
set(NS3_INSTALL_INCLUDE_DIR ${CMAKE_BINARY_DIR}/include/ns3)

# Install headers to the correct location
install(FILES
    ${header_files}
    DESTINATION ${NS3_INSTALL_INCLUDE_DIR}
)
