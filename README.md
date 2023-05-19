# librs232 with CMake Support

## Multiplatform library for serial communications over RS-232 (serial port)

[![Build Status](https://travis-ci.org/srdgame/librs232.svg?branch=master)](https://travis-ci.org/srdgame/librs232)
[![Build status](https://ci.appveyor.com/api/projects/status/github/srdgame/librs232?branch=master&svg=true)](https://ci.appveyor.com/project/srdgame/librs232)

## Changes from the original version
* Windows. Use overlapped IO model.
* Windows. Support detect break/parity error.
* Windows. Implement `rs232_in_queue_clear`.
* Windows. Fix using static variables to support multithreading.
* Windows. `rs232_read` wait at least one byte (like in POSIX version).
* Basic tests on Travis/Appveyor.
* Lua. Export `rs232_in_queue_clear` / `rs232_in_queue` functions.
* Lua. Add more Lua way module.

## Lua binding

```Lua
local rs232 = require "rs232"

local p, e = rs232.port('COM1',{
  baud         = '_9600';
  data_bits    = '_8';
  parity       = 'NONE';
  stop_bits    = '_1';
  flow_control = 'OFF';
  rts          = 'ON';
})

p:open()
print(p:write('AT\r\n'))
print(p:read(64, 5000))
p:close()
```

Original librs232 `README.md` ends here.

## Building and Installing librs232

### Prerequisites

librs232 with CMake Support relies on an installation of [Lua with CMake Support](https://github.com/KritzelKratzel/lua#readme). The same toolchain which has been used with *Lua with CMake Support* is required. librs232 gets all necessary path, header and library information from CMake `liblua` package data and will be installed automatically into the right directory locations - no hassle with `package.path` settings anymore.

**Notice:** Present GNU `autoconf` build structure originates from original librs232 source code and is not used within the context of the CMake-based build infrastructure. Simply ignore it.

### Build and Install

Open `Developer Command Prompt for VS 2022` and change drive and directory. Download and unpack sources or simply clone this repository:

```cmd
c:
cd c:\Temp
git clone https://github.com/KritzelKratzel/librs232
cd librs232
```

CMake strongly encourages out-of-source builds.

```cmd
mkdir build
cd build
cmake .. -G "Visual Studio 17 2022" -A <arch>
cmake --build . --config Release
cmake --install . --config Release
```

Replace `<arch>` with your desired architecture. Available architectures with selected `Visual Studio 17 2022` generator are `Win32`, `x64`, `ARM` and `ARM64`. A librs232 example file is available in `<lua_install_dir>/share/doc/librs232` after install.

## Fetch changes form original librs232 repository

These are the command to incorporate code base changes form original librs232 repository.

1. Add original and remote repository to local clone. This step is only required once.

```cmd
C:\misc\librs232>git remote add librs232 https://github.com/srdgame/librs232.git
C:\misc\librs232>git remote -v
librs232        https://github.com/srdgame/librs232.git (fetch)
librs232        https://github.com/srdgame/librs232.git (push)
origin  git@github.com:KritzelKratzel/librs232.git (fetch)
origin  git@github.com:KritzelKratzel/librs232.git (push)
C:\misc\librs232>
```

2. Command sequence for fetching changes on original repository:

```cmd
git checkout master
git pull origin master
git merge librs232/master
git push origin master
```

