# This source file is part of the Swift.org open source project
#
# Copyright (c) 2014 - 2019 Apple Inc. and the Swift project authors
# Licensed under Apache License v2.0 with Runtime Library Exception
#
# See http://swift.org/LICENSE.txt for license information
# See http://swift.org/CONTRIBUTORS.txt for Swift project authors

# LLBuild currently doesn't export the llbuildSwift target so import it manually for now.
add_library(libllbuildSwift SHARED IMPORTED GLOBAL)

if(LLBUILD_FRAMEWORK)
    set_target_properties(libllbuildSwift
        PROPERTIES
        IMPORTED_LOCATION ${LLBUILD_FRAMEWORK}
    )
else()
    set(LLBUILD_BUILD_DIR ${LLBuild_DIR}/../..)
    set_target_properties(libllbuildSwift
        PROPERTIES
        FRAMEWORK TRUE
        IMPORTED_LOCATION ${LLBUILD_BUILD_DIR}/lib
        INTERFACE_INCLUDE_DIRECTORIES ${LLBUILD_BUILD_DIR}/products/llbuildSwift)
endif()

add_library(SPMLLBuild
  llbuild.swift)

target_link_libraries(SPMLLBuild PUBLIC
  TSCBasic
  TSCUtility
)

if(LLBUILD_FRAMEWORK)
    target_link_libraries(SPMLLBuild PUBLIC -F libllbuildSwift)
else()
	target_link_libraries(SPMLLBuild PUBLIC libllbuild -L libllbuildSwift)
endif()

# NOTE(compnerd) workaround for CMake not setting up include flags yet
set_target_properties(SPMLLBuild PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_Swift_MODULE_DIRECTORY})

if(CMAKE_SYSTEM_NAME STREQUAL Windows)
install(TARGETS SPMLLBuild
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)
endif()
