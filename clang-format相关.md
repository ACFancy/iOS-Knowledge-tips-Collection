### 更新clang-format 二进制文件
 - 到对应release上下载对应的source code [llvm](https://github.com/llvm/llvm-project.git)
 - 确保本地以及安装 cmake 和ninja工具，可以使用breaw进行安装
 - 运行一下脚本
  - Windows step-by-step:
   ```shell
   # [double check you have the tools you need]
    where cmake.exe  # You need to install this.

    # In chromium/src
    tools\win\setenv amd64_x86
    set CLANG_REV=56ac9d30d35632969baa39829ebc8465ed5937ef  # You must change this value (see above)
    rmdir /S /Q llvm-project
    git clone https://github.com/llvm/llvm-project
    cd llvm-project
    git checkout %CLANG_REV%
    mkdir build
    cd build
    set CC=..\..\third_party\llvm-build\Release+Asserts\bin\clang-cl.exe
    set CXX=..\..\third_party\llvm-build\Release+Asserts\bin\clang-cl.exe
    set CFLAGS=-m32
    set CXXFLAGS=-m32
    cmake -G Ninja ..\llvm -DCMAKE_BUILD_TYPE=Release -DLLVM_USE_CRT_RELEASE=MT ^
        -DLLVM_ENABLE_ASSERTIONS=NO -DLLVM_ENABLE_THREADS=NO ^
        -DLLVM_ENABLE_PROJECTS=clang
    ninja clang-format
    bin\clang-format.exe --version
   ```
  - Mac & Linux step-by-step:
    ```shell
    # Check out.
    export CLANG_REV=56ac9d30d35632969baa39829ebc8465ed5937ef   # You must change this value (see above)
    git clone https://github.com/llvm/llvm-project
    cd llvm-project
    git checkout $CLANG_REV
    mkdir build
    cd build

    # On Mac, do the following:
    MACOSX_DEPLOYMENT_TARGET=10.9 cmake -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DLLVM_ENABLE_PROJECTS=clang \
        -DLLVM_ENABLE_ASSERTIONS=NO \
        -DLLVM_ENABLE_TERMINFO=OFF \
        -DLLVM_ENABLE_THREADS=NO \
        -DLLVM_ENABLE_ZLIB=OFF \
        '-DCMAKE_OSX_ARCHITECTURES=arm64;x86_64' \
        ../llvm/

    # On Linux, do the following:
    # Note the relative paths that point to your local Chromium checkout.
    # TODO(thakis): Remove DLLVM_ENABLE_Z3_SOLVER in the next roll. At the pinned
    # revision, Z3 detection does not work with a sysroot, but at LLVM trunk it's
    # already fixed.
    cmake -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DLLVM_ENABLE_PROJECTS=clang \
        -DLLVM_ENABLE_ASSERTIONS=NO \
        -DLLVM_ENABLE_TERMINFO=OFF \
        -DLLVM_ENABLE_THREADS=NO \
        -DLLVM_ENABLE_ZLIB=OFF \
        -DLLVM_ENABLE_Z3_SOLVER=NO \
        -DCMAKE_C_COMPILER=$HOME/src/chrome/src/third_party/llvm-build/Release+Asserts/bin/clang \
        -DCMAKE_CXX_COMPILER=$HOME/src/chrome/src/third_party/llvm-build/Release+Asserts/bin/clang++ \
        -DCMAKE_ASM_COMPILER=$HOME/src/chrome/src/third_party/llvm-build/Release+Asserts/bin/clang \
        -DCMAKE_CXX_STANDARD_LIBRARIES="-static-libgcc -static-libstdc++" \
        -DCMAKE_SYSROOT=$HOME/src/chrome/src/build/linux/debian_sid_amd64-sysroot \
        ../llvm/

    # Finally, build the actual clang-format binary with Ninja
    ninja clang-format
    strip bin/clang-format
    ```
- 参考链接
  - [Google](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/updating_clang_format_binaries.md)
  - [llvm-HomePage-GetStart](https://clang.llvm.org/get_started.html)
