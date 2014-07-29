# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec, David Manura
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( lanes C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )
include ( lua )

include_directories ( src )

install_lua_module ( lanes.core src/lanes.c src/keeper.c src/tools.c src/threading.c )
install_lua_module ( lanes-keeper src/lanes-keeper.lua )
install_lua_module ( lanes src/lanes.lua )

#2DO - use provided bin2c
# Embed keeper.lua in text form in C (embedding bytecode is not LuaJIT2-compatible)
ADD_CUSTOM_COMMAND(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/keeper.lch
  DEPENDS src/keeper.lua
    COMMAND "${LUA}" "${CMAKE_CURRENT_SOURCE_DIR}/tools/bin2c.lua"
            "${CMAKE_CURRENT_SOURCE_DIR}/src/keeper.lua"
            "-o" "${CMAKE_CURRENT_BINARY_DIR}/keeper.lch")
SET_SOURCE_FILES_PROPERTIES(src/lanes.c PROPERTIES OBJECT_DEPENDS
  ${CMAKE_CURRENT_BINARY_DIR}/keeper.lch)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR})


# Build
INCLUDE_DIRECTORIES(src)
ADD_LIBRARY(lua51-lanes MODULE src/lanes.c src/threading.c src/tools.c src/keeper.c)

IF(UNIX AND NOT CYGWIN)
  SET(LIBS pthread)
ENDIF(UNIX AND NOT CYGWIN)



TARGET_LINK_LIBRARIES(lua51-lanes ${LUA_LIBRARY} ${LIBS})
SET_TARGET_PROPERTIES(lua51-lanes PROPERTIES PREFIX "")

# Install all files and documentation
INSTALL (TARGETS lua51-lanes DESTINATION ${INSTALL_CMOD})
INSTALL (FILES src/lanes.lua DESTINATION ${INSTALL_LMOD})

INSTALL (FILES ABOUT BUGS COPYRIGHT CHANGES README TODO DESTINATION ${INSTALL_DATA})
INSTALL (DIRECTORY docs/ DESTINATION ${INSTALL_DOC})
INSTALL (DIRECTORY tests/ DESTINATION ${INSTALL_TEST})
