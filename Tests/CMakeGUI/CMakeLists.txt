include(CMakeParseArguments)

find_package(Qt5Test REQUIRED)

include_directories(
  ${CMake_SOURCE_DIR}/Source
  ${CMake_SOURCE_DIR}/Source/QtDialog
  ${CMake_BINARY_DIR}/Source/QtDialog
  )

set(MOC_SRCS)
qt5_wrap_cpp(MOC_SRCS
  CMakeGUITest.h
  )
add_executable(CMakeGUITest CMakeGUITest.cxx ${MOC_SRCS})
target_link_libraries(CMakeGUITest CMakeGUIMainLib Qt5::Core Qt5::Test Qt5::Widgets)
target_compile_definitions(CMakeGUITest PRIVATE
  "CMakeGUITest_SOURCE_DIR=\"${CMAKE_CURRENT_SOURCE_DIR}\""
  "CMakeGUITest_BINARY_DIR=\"${CMAKE_CURRENT_BINARY_DIR}\""
  )

add_test(NAME CMakeGUI COMMAND ${CMAKE_CMAKE_COMMAND}
  "-DCMakeGUITest_COMMAND=$<TARGET_FILE:CMakeGUITest>"
  "-DCMakeGUITest_SOURCE_DIR=${CMAKE_CURRENT_SOURCE_DIR}"
  "-DCMakeGUITest_BINARY_DIR=${CMAKE_CURRENT_BINARY_DIR}"
  "-DCMakeGUITest_GENERATOR=${CMAKE_GENERATOR}"
  -P "${CMAKE_CURRENT_LIST_DIR}/CMakeGUITest.cmake"
  )

function(add_cmake_gui_lib_test name)
  cmake_parse_arguments(_t "" "" "SOURCES;MOC_SOURCES" ${ARGN})

  set(MOC_SRCS)
  qt5_wrap_cpp(MOC_SRCS
    ${_t_MOC_SOURCES}
    )
  add_executable(${name} ${_t_SOURCES} ${MOC_SRCS})
  target_link_libraries(${name} CMakeGUILib Qt5::Core Qt5::Test Qt5::Widgets)

  add_test(NAME "CMakeGUILib.${name}" COMMAND ${name})
endfunction()

add_cmake_gui_lib_test(QCMakeCacheModel
  SOURCES
    QCMakeCacheModelTest.cxx
    QCMakeCacheModelTest.h
  MOC_SOURCES
    QCMakeCacheModelTest.h
  )
