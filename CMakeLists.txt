# ======================================================================== #
# Copyright 2018-2019 Ingo Wald                                            #
#                                                                          #
# Licensed under the Apache License, Version 2.0 (the "License");          #
# you may not use this file except in compliance with the License.         #
# You may obtain a copy of the License at                                  #
#                                                                          #
#     http://www.apache.org/licenses/LICENSE-2.0                           #
#                                                                          #
# Unless required by applicable law or agreed to in writing, software      #
# distributed under the License is distributed on an "AS IS" BASIS,        #
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. #
# See the License for the specific language governing permissions and      #
# limitations under the License.                                           #
# ======================================================================== #

# Edits made by Elleis Souza for simple optix project compliation for CSC 473

if (POLICY CMP0048)
  cmake_policy(SET CMP0048 NEW)
endif (POLICY CMP0048)

project(testOptixProject)

cmake_minimum_required(VERSION 3.17)
set (CMAKE_CXX_FLAGS "--std=c++11")
set (CUDA_PROPAGATE_HOST_FLAGS ON)

# ------------------------------------------------------------------
# first, include gdt project to do some general configuration stuff
# (build modes, glut, optix, etc)
# ------------------------------------------------------------------
set(gdt_dir ${PROJECT_SOURCE_DIR}/common/gdt/)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${gdt_dir}/cmake/")
include(${gdt_dir}/cmake/configure_build_type.cmake)
include(${gdt_dir}/cmake/configure_optix.cmake)

#set(glfw_dir ${PROJECT_SOURCE_DIR}/submodules/glfw/)
#include(${gdt_dir}/cmake/configure_glfw.cmake)

mark_as_advanced(CUDA_SDK_ROOT_DIR)

# ------------------------------------------------------------------
# import gdt submodule
# ------------------------------------------------------------------
include_directories(${gdt_dir})
add_subdirectory(${gdt_dir} EXCLUDE_FROM_ALL)

# ------------------------------------------------------------------
# build glfw
# ------------------------------------------------------------------
set(OpenGL_GL_PREFERENCE LEGACY)
if (WIN32)
#  set(glfw_dir ${PROJECT_SOURCE_DIR}/submodules/glfw/)
  set(glfw_dir ${PROJECT_SOURCE_DIR}/common/3rdParty/glfw/)
  include_directories(${glfw_dir}/include)
  add_subdirectory(${glfw_dir} EXCLUDE_FROM_ALL)
else()
  find_package(glfw3 REQUIRED)
endif()
include_directories(common)
add_subdirectory(common/glfWindow EXCLUDE_FROM_ALL)


# ------------------------------------------------------------------
# and final build rules for the project
# ------------------------------------------------------------------

set(optix_LIBRARY "")

find_package(OpenGL REQUIRED)

include_directories(${OptiX_INCLUDE})

cuda_compile_and_embed(embedded_ptx_code ${CMAKE_SOURCE_DIR}/src/devicePrograms.cu)

#file(GLOB_RECURSE SOURCES "${CMAKE_SOURCE_DIR}/src/*.cc" "${CMAKE_SOURCE_DIR}/ext/*/*.cpp" "${CMAKE_SOURCE_DIR}/ext/glad/src/*.c")

AUX_SOURCE_DIRECTORY(src/ SOURCES) 

add_executable(testOptiXProject
  ${embedded_ptx_code}
  ${SOURCES}
  )

target_link_libraries(testOptiXProject
  gdt
  # optix dependencies, for rendering
  ${optix_LIBRARY}
  ${CUDA_LIBRARIES}
  ${CUDA_CUDA_LIBRARY}
  # glfw and opengl, for display
  glfWindow
  glfw
  ${OPENGL_gl_LIBRARY}
  )
