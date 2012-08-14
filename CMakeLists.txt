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

install_data ( ABOUT BUGS COPYRIGHT CHANGES README TODO )
install_doc ( docs/ )
install_test ( tests/ )
