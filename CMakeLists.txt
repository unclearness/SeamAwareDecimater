cmake_minimum_required(VERSION 3.13)

project(mesh_decimator LANGUAGES CXX VERSION 0.0.1 DESCRIPTION "mesh decimator")

set(CMAKE_VERBOSE_MAKEFILE TRUE)

# .lib
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib)

# .dll and .exe
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin) 


set(EIGEN_INSTALL_DIR "third_party/libigl/external/eigen" CACHE PATH "eigen installed directory")
if(NOT DEFINED EIGEN_INSTALL_DIR)
  set(EIGEN_INSTALL_DIR "third_party/eigen" CACHE PATH "eigen installed directory")
message("EIGEN_INSTALL_DIR: ${EIGEN_INSTALL_DIR}") 
endif()

## We don't have/want MOSEK
add_definitions(-DIGL_NO_MOSEK)

# use libigl as static lib
set(LIBIGL_WITH_COMISO OFF CACHE BOOL           "Use CoMiso"                   FORCE)
set(LIBIGL_WITH_EMBREE OFF CACHE BOOL           "Use Embree"                   FORCE)
set(LIBIGL_WITH_OPENGL OFF CACHE BOOL           "Use OpenGL"                   FORCE)
set(LIBIGL_WITH_OPENGL_GLFW OFF CACHE BOOL      "Use GLFW"                     FORCE)
set(LIBIGL_WITH_OPENGL_GLFW_IMGUI OFF CACHE BOOL "Use ImGui"                    FORCE)
set(LIBIGL_WITH_PNG OFF CACHE BOOL              "Use PNG"                      FORCE)
set(LIBIGL_WITH_TETGEN OFF CACHE BOOL           "Use Tetgen"                   FORCE)
set(LIBIGL_WITH_TRIANGLE OFF CACHE BOOL         "Use Triangle"                 FORCE)
set(LIBIGL_WITH_PREDICATES OFF CACHE BOOL       "Use exact predicates"         FORCE)
set(LIBIGL_WITH_XML OFF CACHE BOOL              "Use XML"                      FORCE)
set(MSVC_RUNTIME "dynamic" CACHE STRING "MSVS runtime" FORCE) # MD
add_definitions(-DWIN32)
add_definitions(-DIGL_STATIC_LIBRARY)
add_subdirectory(third_party/libigl)

include_directories(
    include
    third_party
    third_party/libigl/include
    ${EIGEN_INSTALL_DIR}
    ${STB_INSTALL_DIR}
    ${TINYOBJLOADER_INSTALL_DIR}
    )

add_library(mesh_decimator
    STATIC
    quadric_error_metric.h
    cost_and_placement.h
    half_edge.h
    neighbor_faces_and_boundary.h
    detect_foldover.h
    decimate.h
    collapse_edge_seam.h
    eiquadprog.h
    pythonlike.h

    quadric_error_metric.cpp
    cost_and_placement.cpp
    half_edge.cpp
    neighbor_faces_and_boundary.cpp
    detect_foldover.cpp
    decimate.cpp
    collapse_edge_seam.cpp
    )
    
add_executable(mesh_decimator_cmd
	decimater.cpp
	)
target_link_libraries (mesh_decimator_cmd
  igl
  mesh_decimator
)
