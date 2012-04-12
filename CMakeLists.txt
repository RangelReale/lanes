# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec, David Manura
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( lanes C )
cmake_minimum_required ( VERSION 2.6 )
include ( cmake/dist.cmake )
include ( cmake/lua.cmake )

#2DO - patch threading.c to support cygwin.
# The following values are just a guess.
# WARNING: test segfault under Cygwin
if ( CYGWIN )
  add_definitions ( -D_PRIO_MODE=SCHED_FIFO )
  add_definitions ( -D_PRIO_HI=15 ) # maximum that doesn't crash
  add_definitions ( -D_PRIO_0=0 )
  add_definitions ( -D_PRIO_LO=-15 ) # ???
  add_definitions ( -Dpthread_yield=sched_yield )
endif()

#2DO: do this from dist.cmake instead.
# Embed keeper.lua in text form in C (embedding bytecode may not LuaJIT2-compatible)
find_program ( LUA NAMES lua lua.bat )
add_custom_command ( OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/keeper.lch
  DEPENDS src/keeper.lua
  COMMAND "${LUA}" "${CMAKE_CURRENT_SOURCE_DIR}/tools/bin2c.lua"
          "${CMAKE_CURRENT_SOURCE_DIR}/src/keeper.lua"
          "-o" "${CMAKE_CURRENT_BINARY_DIR}/keeper.lch")
set_source_files_properties ( src/lanes.c PROPERTIES OBJECT_DEPENDS
  ${CMAKE_CURRENT_BINARY_DIR}/keeper.lch )
include_directories ( ${CMAKE_CURRENT_BINARY_DIR} )

install_lua_module ( lua51-lanes src/lanes.c src/threading.c src/tools.c src/keeper.c )
if ( UNIX AND NOT CYGWIN )
  set ( LIBS pthread )
endif()
target_link_libraries ( lua51-lanes ${LUA_LIBRARY} ${LIBS} )

install_lua_module ( lanes src/lanes.lua )

install_data ( ABOUT BUGS COPYRIGHT CHANGES README TODO  )
install_doc ( docs/ )
install_test ( tests/ )

#2DO: these hang on Ubuntu
configure_file ( src/lanes.lua ${CMAKE_CURRENT_BINARY_DIR}/lanes.lua ) # for testing
#add_lua_test ( tests/errhangtest.lua )
#add_lua_test ( tests/irayo_recursive.lua )
#add_lua_test ( tests/irayo_closure.lua )
#add_lua_test ( tests/basic.lua )
#add_lua_test ( tests/fifo.lua )
#add_lua_test ( tests/keeper.lua )
#add_lua_test ( tests/timer.lua )
#add_lua_test ( tests/atomic.lua )
#add_lua_test ( tests/cyclic.lua )
#add_lua_test ( tests/objects.lua )
#add_lua_test ( tests/fibonacci.lua )
#add_lua_test ( tests/recursive.lua )
#add_lua_test ( tests/func_is_string.lua )
#add_lua_test ( tests/atexit.lua )
#add_lua_test ( tests/linda_perf.lua )
