#Copyright (C) 2014-2018 Nickolai  Vostrikov
#
#Licensed to the Apache Software Foundation( ASF ) under one
#or more contributor license agreements.See the NOTICE file
#distributed with this work for additional information
#regarding copyright ownership.The ASF licenses this file
#to you under the Apache License, Version 2.0 ( the
#	"License" ); you may not use this file except in compliance
#	with the License.You may obtain a copy of the License at
#
#	http ://www.apache.org/licenses/LICENSE-2.0
#	or read LICENSE file in root repository directory
#
#Unless required by applicable law or agreed to in writing,
#software distributed under the License is distributed on an
#"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
#KIND, either express or implied.See the License for the
#specific language governing permissions and limitations
#under the License.

cmake_minimum_required( VERSION 3.8 FATAL_ERROR )

if( NOT DEFINED BUILD_AS_SUBPROJECT )
    project( build-icu-cmake VERSION 0.0.1 )
    # C/C++ languages required.
    enable_language( C )
    enable_language( CXX )
    # Force C++11 support requirement by ICU
    set( CMAKE_CXX_STANDARD 11 )
    set( CMAKE_CXX_STANDARD_REQUIRED ON )

    # Adapted from LLVM's toplevel CMakeLists.txt file
    if( CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR )
        message(FATAL_ERROR "
            In-source builds are not allowed. CMake would overwrite the
            makefiles distributed with utf8proc. Please create a directory
            and run cmake from there. Building in a subdirectory is
            fine, e.g.:
            
            mkdir build
            cd build
            cmake ..
            
            This process created the file `CMakeCache.txt' and the
            directory `CMakeFiles'. Please delete them.
            
            ")
    endif()
endif()

set( BINDIR "${PROJECT_BINARY_DIR}" )
set( BUILDDIR "${BINDIR}/_icu-build-dir" )
set( DIST_DIR "${BUILDDIR}/icu-dist" CACHE PATH INTERNAL )

if( EXISTS ${BUILDDIR} )
    file( REMOVE_RECURSE ${BUILDDIR} )
endif()

file( MAKE_DIRECTORY ${BUILDDIR} )

set( BUILD_ICU_DIR "${BUILDDIR}/icu/source" )

if( "${CMAKE_SIZEOF_VOID_P}" STREQUAL "8" )
    set( ICU_WITH_LIB_BITS "-with-library-bits=64" )
elseif( "${CMAKE_SIZEOF_VOID_P}" STREQUAL "4" )
    set( ICU_WITH_LIB_BITS "-with-library-bits=32" )    
else()
    message( FATAL_ERROR "Unexpected architecture, sizeof(void*): ${CMAKE_SIZEOF_VOID_P}" )
endif()

if( WIN32 )
    include( ${CMAKE_CURRENT_LIST_DIR}/MakeForWindows.txt )
elseif( LINUX )
    include( ${CMAKE_CURRENT_LIST_DIR}/MakeForLinux.txt )
else()
    message( FATAL_ERROR "Unknown OS" )
endif()

set( ICU_DISR_DIR "${DIST_DIR}" CACHE PATH INTERNAL )



