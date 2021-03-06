#
# Copyright (c) 2017 Bitprim developers (see AUTHORS)
#
# This file is part of Bitprim.
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU Affero General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU Affero General Public License for more details.
#
# You should have received a copy of the GNU Affero General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

cmake_minimum_required(VERSION 3.4)

# bitprim
#==============================================================================
project(bitprim
  VERSION 3.0.0
  LANGUAGES CXX C)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(CURRENCY "BCH" CACHE STRING "Specify the Cryptocurrency (BCH|BTC|LTC).")
if (${CURRENCY} STREQUAL "BCH")
    add_definitions(-DBITPRIM_CURRENCY_BCH)
elseif (${CURRENCY} STREQUAL "BTC")
    add_definitions(-DBITPRIM_CURRENCY_BTC)
elseif (${CURRENCY} STREQUAL "LTC")
    add_definitions(-DBITPRIM_CURRENCY_LTC)
else()
    message(FATAL_ERROR "Invalid Cryptocurrency: ${CURRENCY}")
endif()

message(STATUS "Bitprim: Cryptocurrency: ${CURRENCY}")
# Check for baseline language coverage in the compiler for the C++11 standard.
#------------------------------------------------------------------------------
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Options
# ---------------------------------------------------------------
option(USE_CONAN "Use Conan Build Tool." OFF)
option(NO_CONAN_AT_ALL "Conan totally disabled." OFF)

option(ENABLE_POSITION_INDEPENDENT_CODE "Enable POSITION_INDEPENDENT_CODE property" ON)
option(WITH_CONSOLE "Compile console application." OFF)
set(WITH_BIGNUM "no" CACHE STRING "Specify Bignum Implementation (gmp|no|auto).")

set(USE_CPP11_ABI ON)
set(ENABLE_POSITION_INDEPENDENT_CODE ON)
set(USE_CONAN OFF)
set(NO_CONAN_AT_ALL OFF)

set(WITH_CONSOLE OFF)

if (NO_CONAN_AT_ALL)
  set(USE_CONAN OFF)
endif()

# ---------------------------------------------------------------

option(WITH_RPC "Compile with RPC-ZMQ support." ON)
option(WITH_REMOTE_BLOCKCHAIN "Compile with remote bitprim-blockchain (aka. bitprim-blockchain-requester)." OFF)
option(WITH_REMOTE_DATABASE "Compile with remote bitprim-database (aka. bitprim-database-requester)." OFF)
option(WITH_LOCAL_MINING "Compile with local mining." OFF)
option(ENABLE_TEST "Compile with enable test." OFF)
option(ENABLE_TESTS "Compile with enable test." OFF)
option(ENABLE_MODULE_RECOVERY "Compile with enable module recovery." ON)
option(WITH_TOOLS "Compile with tools." OFF)
option(WITH_CONSOLE_NODE_CINT "" OFF)

# Implement --with-tests and declare WITH_TESTS.
#------------------------------------------------------------------------------
option(WITH_TESTS "Compile with unit tests." OFF)

# Implement --with-examples and declare WITH_EXAMPLES.
#------------------------------------------------------------------------------
option(WITH_EXAMPLES "Compile with examples." OFF)

# Inherit --enable-shared and define BOOST_TEST_DYN_LINK.
#------------------------------------------------------------------------------
option(ENABLE_SHARED "" OFF)
option(ENABLE_SHARED_NODE_CINT "" ON)

# Make sure module recovery is on on secp
#------------------------------------------------------------------------------
set(ENABLE_MODULE_RECOVERY ON)

if (WITH_RPC)
    add_definitions(-DBITPRIM_WITH_RPC)
endif()

if (WITH_LOCAL_MINING)
	add_definitions(-DWITH_LOCAL_MINING)
endif()

# Bitprim tools
#------------------------------------------------------------------------------
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
include(BitprimTools)

# Build
#==============================================================================
if (NOT DEFINED ENABLE_MODULE_RECOVERY)
  set(ENABLE_MODULE_RECOVERY ON CACHE BOOL "Enable ECDSA pubkey recovery module.")
endif()

add_subdirectory(secp256k1)
add_subdirectory(bitprim-core)
add_subdirectory(bitprim-consensus)
add_subdirectory(bitprim-network)
add_subdirectory(bitprim-database)
add_subdirectory(bitprim-blockchain)
add_subdirectory(bitprim-node)
add_subdirectory(bitprim-rpc)
add_subdirectory(bitprim-node-exe)
add_subdirectory(bitprim-node-cint)

# Tests
#==============================================================================
if (WITH_TESTS)
  enable_testing()
endif()
