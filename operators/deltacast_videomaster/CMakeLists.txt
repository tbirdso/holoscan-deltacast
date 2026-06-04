# SPDX-FileCopyrightText: Copyright (c) 2022-2026, DELTACAST.TV. All rights reserved.
# SPDX-License-Identifier: Apache-2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

include(FetchContent)

# Adds the operator directory itself to CMAKE_MODULE_PATH so that a
# FindVideoMasterHD.cmake placed alongside this file is discoverable.
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR})

FetchContent_Declare(
    VideoMasterAPIHelper
    GIT_REPOSITORY  https://github.com/deltacasttv/videomaster-api-helper.git
    GIT_TAG         1.3.0
)
FetchContent_MakeAvailable(VideoMasterAPIHelper)

find_package(VideoMasterHD REQUIRED)

add_library(videomaster SHARED
  videomaster_base.hpp
  videomaster_base.cpp
  videomaster_source.hpp
  videomaster_source.cpp
  videomaster_transmitter.hpp
  videomaster_transmitter.cpp
)
add_library(holoscan::videomaster ALIAS videomaster)

target_include_directories(videomaster
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        # VideoMasterAPIHelper and VideoMasterHD headers are needed by the
        # installed public headers; expose them at build time only.
        # Downstream installs are expected to provide VideoMasterHD themselves.
        $<BUILD_INTERFACE:$<TARGET_PROPERTY:VideoMasterAPIHelper,INTERFACE_INCLUDE_DIRECTORIES>>
        $<BUILD_INTERFACE:$<TARGET_PROPERTY:VideoMasterHD::Core,INTERFACE_INCLUDE_DIRECTORIES>>
        $<INSTALL_INTERFACE:include>)
# Propagate the compile guard so consumers (#ifdef DELTACAST_VIDEOMASTER in main.cpp)
# pick it up automatically when they link this target.
target_compile_definitions(videomaster INTERFACE DELTACAST_VIDEOMASTER)
target_link_libraries(videomaster
  # VideoMasterAPIHelper and VideoMasterHD::Core are FetchContent/built targets;
  # exporting them would require adding them to the export set. Since videomaster
  # is a shared library they are already linked in, so PRIVATE is correct here.
  PRIVATE
    VideoMasterAPIHelper
    VideoMasterHD::Core
  PUBLIC
    CUDA::cudart
    CUDA::cuda_driver
    GXF::multimedia
    GXF::std
    holoscan::core
)

install(TARGETS videomaster
    EXPORT holoscan_deltacast_targets
    LIBRARY DESTINATION lib
    COMPONENT runtime)

install(FILES
    videomaster_base.hpp
    videomaster_source.hpp
    videomaster_transmitter.hpp
    DESTINATION include/holoscan/deltacast
    COMPONENT runtime)

add_subdirectory(python)
