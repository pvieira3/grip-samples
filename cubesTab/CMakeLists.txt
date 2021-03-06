# ***********************************
# CMakeLists.txt for cubesTab
# 2013-01-15
# ***********************************
cmake_minimum_required (VERSION 2.6)
project (cubesTab)

message (STATUS "Creating Makefile for cubesTab")

# Setting CMake flags
if(WIN32)
  set (CMAKE_INSTALL_PREFIX "C:/Golems" CACHE PATH "Install prefix" FORCE)
  message (STATUS "Setup Visual Studio Specific Flags")
  set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MDd /Zi /GL /Gy /W1 /EHsc")
  set (CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MD /Zi /GL /Gy /W1 /EHsc")
  set (CMAKE_DEBUG_POSTFIX "d")
  set (CMAKE_EXE_LINKER_FLAGS "/LTCG")
  set (CMAKE_MODULE_LINKER_FLAGS "/LTCG")
else ()
  set (CMAKE_INSTALL_PREFIX /usr )
  # Optimization enabled
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse2 -O3  -std=c++0x")
  set(CMAKE_CXX_FLAGS_PROFILE "-pg" CACHE STRING "Profiling flags")
endif()

# Include DART and GRIP config files
include (${CMAKE_INSTALL_PREFIX}/share/dart/DARTConfig.cmake)
include (${CMAKE_INSTALL_PREFIX}/share/grip/GRIPConfig.cmake)

include_directories(${DART_INCLUDEDIR})
include_directories(${GRIP_INCLUDEDIR})
link_directories(${DART_LIBRARY_DIRS})

# OpenGL and GLUR required for 
Find_Package(OpenGL REQUIRED)
Find_Package(GLUT REQUIRED)
INCLUDE_DIRECTORIES( ${GLUT_INCLUDE_DIRS} )

# Search all header and source files
set ( cubesTab_src cubesTab.cpp cubesTabApp.cpp )
set ( cubesTab_hdr cubesTab.h )

# Set wxWidgets-related parameters
if(WIN32)
  set (wxWidgets_EXCLUDE_COMMON_LIBRARIES TRUE)
  set (wxWidgets_USE_LIBS base core gl png tiff jpeg zlib)
else(WIN32)
  set (wxWidgets_USE_LIBS base core gl)
endif(WIN32)

# Search wxWidgets
find_package (wxWidgets)
if (wxWidgets_FOUND)
  include (${wxWidgets_USE_FILE})
  include_directories(${DART_INCLUDE_DIRS} ${GRIP_HEADERS} ${WXWIDGETS_SHARED_INCLUDE_DIRS})
  link_directories(${CMAKE_SOURCE_DIR}/lib ${GRIP_LIBDIR} ${WXWIDGETS_SHARED_LIBRARY_DIRS} )

  # If wxWidgets found, create the executable
  if(MSVC)
    add_executable (cubesTab WIN32 ${cubesTab_src} ${cubesTab_hdr})
    set_target_properties ( cubesTab PROPERTIES LINK_FLAGS "/SUBSYSTEM:WINDOWS")
  else()
    add_executable (cubesTab ${cubesTab_src} ${cubesTab_hdr})
  endif()

  # Link to GRIP, DART and wxWidgets
  target_link_libraries (cubesTab ${GRIP_LIBRARIES} ${DART_LIBRARIES} ${wxWidgets_LIBRARIES} ${Boost_LIBRARIES})

else (wxWidgets_FOUND)
  message ("wxWidgets not found!")
endif (wxWidgets_FOUND)



