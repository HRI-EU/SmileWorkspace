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
STRING(LENGTH "${CMAKE_CURRENT_SOURCE_DIR}/" RCS_BASE_PATH_LENGTH)
ADD_DEFINITIONS("-DRCS_BASE_PATH_LENGTH=${RCS_BASE_PATH_LENGTH}")

################################################################################
# Override some option settings to reduce dependencies
################################################################################
OPTION (RCS_COPY_OR_LINK_CONFIG_DIRS "Copy or link configuration file directories" OFF)
OPTION (TROPIC_COPY_OR_LINK_CONFIG_DIRS "Copy or link configuration file directories" OFF)
OPTION (Eigen3_FOUND "Override default Eigen3_FOUND option" OFF)
OPTION (USE_EIGEN3 "Override default USE_EIGEN3 option" OFF)
OPTION (USE_VORTEX "Override default USE_VORTEX option" OFF)
OPTION (USE_MUJOCO "Override default USE_MUJOCO option" OFF)
OPTION (octomap_FOUND "Override default octomap_FOUND option" OFF)
OPTION (USE_OCTOMAP "Override default USE_OCTOMAP option" OFF)
OPTION (QPOASES_FOUND "Override default QPOASES_FOUND option" OFF)
OPTION (USE_QPOASES "Override default USE_QPOASES option" OFF)
OPTION (USE_WM5 "Override default USE_WM5 option" ON)

IF (IS_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/AzureKinectPerception")
  OPTION (WITH_AZURE_DK "Compile against Azure Kinect DK" OFF)
ENDIF()

SET (WM5_FOUND ON)
SET (WM5_LIBRARIES Wm5Core Wm5Mathematics)

################################################################################
# All submodules go here
################################################################################

ADD_SUBDIRECTORY(WildMagic5p17)
ADD_SUBDIRECTORY(Rcs)
LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/Rcs/cmake)

IF(WITH_AZURE_DK)
  ADD_SUBDIRECTORY(AzureKinectPerception)
ENDIF()

ADD_SUBDIRECTORY(Tropic)
ADD_SUBDIRECTORY(ESLib)
ADD_SUBDIRECTORY(AffAction)
