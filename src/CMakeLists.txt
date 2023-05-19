# MIT License
#
# Copyright (c) 2023 Kritzel Kratzel.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy of
# this software and associated documentation files (the "Software"), to deal in 
# the Software without restriction, including without limitation the rights to 
# use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
# the Software, and to permit persons to whom the Software is furnished to do so,
# subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all 
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

# ------------------------------------------------------------------------------
# rs232 DLL
add_library(rs232 SHARED)
# librs232 source code structure is cluttered an needs special handling within
# the context of CMake integration.
set(RS232_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
set(RS232_BINDING_DIR ${PROJECT_SOURCE_DIR}/bindings/lua)
target_include_directories(rs232 PRIVATE
  ${LIBLUA_INCLUDEDIR}
  ${RS232_INCLUDE_DIR})
# setup platform-specific sources, compile and linker options
if(WIN32 AND NOT MinGW)
  target_sources(rs232 PRIVATE
    rs232_windows.c ${RS232_INCLUDE_DIR}/${PROJECT_NAME}/rs232_windows.h)  
  target_compile_options(rs232 PRIVATE
    /D_USRDLL /D_CRT_SECURE_NO_WARNINGS /DRS232_EXPORT)
  if(LIBRS232_DEBUG_MODE)
    target_compile_options(rs232 PRIVATE /DRS232_DEBUG)
  endif()
  target_link_options(rs232 PRIVATE
    /LIBPATH:${LIBLUA_LIBDIR} liblua.lib ws2_32.lib)
else()
  message(FATAL_ERROR "Not yet fully implemented.")
  target_sources(rs232 PRIVATE
    rs232_posix.c ${RS232_INCLUDE_DIR}/${PROJECT_NAME}/rs232_posix.h)
endif()
# add more sources
target_sources(rs232 PRIVATE
  rs232.c ${RS232_INCLUDE_DIR}/${PROJECT_NAME}/rs232.h ${RS232_BINDING_DIR}/luars232.c)

# Account for specific DLL naming conventions.
set_target_properties(rs232 PROPERTIES OUTPUT_NAME "core")
# Install
install(
  TARGETS rs232
  RUNTIME DESTINATION ${INSTALL_LIBRS232_CDIR}
)

# ------------------------------------------------------------------------------
# install lua files to defined locations
install(
  FILES ${RS232_BINDING_DIR}/rs232.lua
  DESTINATION ${INSTALL_TOP_LDIR}
)
