cmake_minimum_required(VERSION 3.5)

# ===========================================================================
# Project Information
# ===========================================================================
project(
  tksvg
  LANGUAGES C
  VERSION 0.14)

# ===========================================================================
# Locate Tcl/Tk
# ===========================================================================
find_package(TCL REQUIRED)
find_package(TclStub REQUIRED)

# ===========================================================================
# Target: tksvg shared library
# ===========================================================================
add_library(tksvg SHARED third_party/tksvg/generic/tkImgSVG.c)
target_link_libraries(tksvg PRIVATE ${TCL_STUB_LIBRARY})
target_link_libraries(tksvg PRIVATE ${TK_STUB_LIBRARY})

target_compile_definitions(tksvg PRIVATE -DUSE_TCL_STUBS)
target_compile_definitions(tksvg PRIVATE -DUSE_TK_STUBS)
target_compile_definitions(tksvg PRIVATE -DPACKAGE_NAME="${CMAKE_PROJECT_NAME}")
target_compile_definitions(tksvg PRIVATE -DPACKAGE_VERSION="${CMAKE_PROJECT_VERSION}")
target_include_directories(tksvg PRIVATE ${TCL_INCLUDE_PATH})
target_include_directories(tksvg PRIVATE ${TK_INCLUDE_PATH})
target_include_directories(tksvg PRIVATE third_party/nanosvg/src)

block()
set(PACKAGE_NAME ${CMAKE_PROJECT_NAME})
set(PACKAGE_VERSION ${CMAKE_PROJECT_VERSION})
set(PKG_LIB_FILE ${CMAKE_SHARED_LIBRARY_PREFIX}tksvg${CMAKE_SHARED_LIBRARY_SUFFIX})
configure_file(third_party/tksvg/pkgIndex.tcl.in pkgIndex.tcl @ONLY)
endblock()

if(WIN32)
  install(TARGETS tksvg RUNTIME DESTINATION tksvg)
else()
  install(TARGETS tksvg LIBRARY DESTINATION tksvg)
endif()
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/pkgIndex.tcl DESTINATION tksvg)
