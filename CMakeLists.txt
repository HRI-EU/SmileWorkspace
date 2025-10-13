################################################################################
#
#  Copyright (c) Honda Research Institute Europe GmbH.
#  All rights reserved.
#
#  Redistribution and use in source and binary forms, with or without
#  modification, are permitted provided that the following conditions are met:
#
#  1. Redistributions of source code must retain the above copyright notice,
#     this list of conditions and the following disclaimer.
#
#  2. Redistributions in binary form must reproduce the above copyright notice,
#     this list of conditions and the following disclaimer in the documentation
#     and/or other materials provided with the distribution.
#
#  3. Neither the name of the copyright holder nor the names of its
#     contributors may be used to endorse or promote products derived from
#     this software without specific prior written permission.
#
#  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDER "AS IS" AND ANY EXPRESS OR
#  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES
#  OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED.
#  IN NO EVENT SHALL THE COPYRIGHT HOLDER BE LIABLE FOR ANY DIRECT, INDIRECT,
#  INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
#  LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA,
#  OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
#  LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
#  NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE,
#  EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#
################################################################################
CMAKE_MINIMUM_REQUIRED(VERSION 3.4)
PROJECT(SmileWorkspace)
SET(BST_INSTALL_CATEGORY Libraries) 

SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

LIST(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/src/Rcs/cmake)


################################################################################
# Macro to copy directories
################################################################################
MACRO(COPY_CONFIG_DIR CFG_FROM CFG_TO_BASEDIR CFG_TO_SUBDIRNAME)

SET(CFG_TO ${CFG_TO_BASEDIR}/${CFG_TO_SUBDIRNAME})

IF (WIN32 AND (NOT IS_DIRECTORY "${CFG_TO}") AND (EXISTS "${CFG_TO}"))
  MESSAGE(STATUS "Removing non-directory ${CONFIG_DST_DIR}")
  FILE(REMOVE ${CONFIG_DST_DIR})
ENDIF()

IF(NOT EXISTS "${CFG_TO}")

  IF (NOT EXISTS ${CFG_TO_BASEDIR})
    FILE(MAKE_DIRECTORY ${CFG_TO_BASEDIR})
  ENDIF()
  
  IF(UNIX)
    SET(CONFIGDIR_COPY_COMMAND create_symlink)
    MESSAGE(STATUS "Linking config directory from ${CFG_FROM} to ${CFG_TO}")
  ELSE()
    SET(CONFIGDIR_COPY_COMMAND copy_directory)
    MESSAGE(STATUS "Copying config directory from ${CFG_FROM} to ${CFG_TO}")
  ENDIF()

  EXECUTE_PROCESS (COMMAND
   ${CMAKE_COMMAND} -E ${CONFIGDIR_COPY_COMMAND} ${CFG_FROM} ${CFG_TO})
ELSE()
  MESSAGE(STATUS "Config directory ${CFG_TO} already exists - skipping")
ENDIF()

ENDMACRO()

################################################################################
# Link / copy mesh directories 
################################################################################
INCLUDE(CopyOrLinkConfig)
RCS_COPY_OR_LINK_CONFIG("${PROJECT_SOURCE_DIR}/config/meshes/kinova_description" "${PROJECT_BINARY_DIR}/config/kinova_description")
RCS_COPY_OR_LINK_CONFIG("${PROJECT_SOURCE_DIR}/config/meshes/hri_scitos_description" "${PROJECT_BINARY_DIR}/config/hri_scitos_description")
RCS_COPY_OR_LINK_CONFIG("${PROJECT_SOURCE_DIR}/config/meshes/assets" "${PROJECT_BINARY_DIR}/config/assets")
RCS_COPY_OR_LINK_CONFIG("${PROJECT_SOURCE_DIR}/config/meshes/kortex_description" "${PROJECT_BINARY_DIR}/config/kortex_description")
RCS_COPY_OR_LINK_CONFIG("${PROJECT_SOURCE_DIR}/src/Rcs/config/fonts" "${PROJECT_BINARY_DIR}/config/fonts")




#COPY_CONFIG_DIR("${CMAKE_SOURCE_DIR}/config/meshes/kinova_description" "${PROJECT_BINARY_DIR}/config" "kinova_description")
#COPY_CONFIG_DIR("${CMAKE_SOURCE_DIR}/config/meshes/hri_scitos_description" "${PROJECT_BINARY_DIR}/config" "hri_scitos_description")
#COPY_CONFIG_DIR("${CMAKE_SOURCE_DIR}/config/meshes/assets" "${PROJECT_BINARY_DIR}/config" "assets")
#COPY_CONFIG_DIR("${CMAKE_SOURCE_DIR}/config/meshes/kortex_description" "${PROJECT_BINARY_DIR}/config" "kortex_description")
#COPY_CONFIG_DIR("${CMAKE_SOURCE_DIR}/src/Rcs/config/fonts" "${PROJECT_BINARY_DIR}/config" "fonts")

################################################################################
# All submodules go here
################################################################################
ADD_SUBDIRECTORY(src)

################################################################################
# Installation
################################################################################
INSTALL(
 DIRECTORY config/meshes/
 DESTINATION config
)

# $<CONFIG> expands to the build type, like Release etc., but only at install time
IF (WIN32)
  INSTALL(DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>/"
          DESTINATION bin
          FILES_MATCHING PATTERN "*.dll")
ENDIF()
