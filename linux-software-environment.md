# 如何在 Linux 下安装软件

很多情况下，我们需要在 Linux 下安装一些软件才能做好一些事情，本部分内容就只想说清楚一个事情：如何在Linux下安装一个软件。

在本教程中，主要以cmake、gflags作为例子进行说明。

## 一个简要的提纲

粗略的分类，一般安装软件的方法有这两种：

1. 使用包管理器一键安装
   1. 如 Ubuntu 使用 `apt` 来安装， CentOS 使用 `yum` 来安装
2. 得到源码，手动编译安装

## 使用包管理器进行安装

先使用命令`yum search XXXX`查询包的名字。

例子：gflags

```
$ sudo yum search gflags
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.163.com
 * epel: my.mirrors.thegigabit.com
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.163.com
============================================================================= N/S matched: gflags ==============================================================================
gflags-devel.x86_64 : Development files for gflags
gflags.x86_64 : Library for commandline flag processing
python2-gflags.noarch : Commandline flags module for Python 2

  Name and summary matches only, use "search all" for everything.

```

例子：cmake

```
$ sudo yum search cmake
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.163.com
 * epel: my.mirrors.thegigabit.com
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.163.com
============================================================================== N/S matched: cmake ==============================================================================
cmake-fedora.noarch : CMake helper modules for fedora developers
cmake-gui.x86_64 : Qt GUI for cmake
cmake3-data.noarch : Common data-files for cmake3
cmake3-doc.noarch : Documentation for cmake3
cmake3-gui.x86_64 : Qt GUI for cmake3
extra-cmake-modules.noarch : Additional modules for CMake build system
python36-colcon-cmake.noarch : Extension for colcon to support CMake packages
cmake.x86_64 : Cross-platform make system
cmake3.x86_64 : Cross-platform make system
leatherman.x86_64 : Collection of C++ and CMake utility libraries

  Name and summary matches only, use "search all" for everything.
  
```

后面我们就可以使用 `yum install XXXX` 进行安装。


我们下面研究两个小问题：

1. gflags 和 gflags-devel 这两个包有什么区别？（ubuntu/debian下是 gflags 和 gflags-dev）
2. yum install 安装一个软件的时候，究竟在做什么？

## 包管理器做了什么事情？

yum install 做了啥？ 实际就是将rpm包中的文件复制到服务器上指定目录。我们可以通过[这种方式](https://www.cyberciti.biz/faq/rhel-centos-fedora-list-contents-of-package-using-yum-command/)
来查看安装指定包时安装了什么文件。


```
sudo yum install yum-utils
repoquery --list ${name of package}
```

例子：gflags 和 gflags-devel 包



```
$ repoquery --list gflags
/usr/bin/gflags_completions.sh
/usr/lib64/libgflags.so.2.1
/usr/lib64/libgflags_nothreads.so.2.1
/usr/share/doc/gflags-2.1.1
/usr/share/doc/gflags-2.1.1/AUTHORS.txt
/usr/share/doc/gflags-2.1.1/COPYING.txt
/usr/share/doc/gflags-2.1.1/ChangeLog.txt
/usr/share/doc/gflags-2.1.1/README.txt
$ repoquery --list gflags-devel
/usr/include/gflags
/usr/include/gflags/gflags.h
/usr/include/gflags/gflags_completions.h
/usr/include/gflags/gflags_declare.h
/usr/lib64/cmake
/usr/lib64/cmake/gflags
/usr/lib64/cmake/gflags/gflags-config-version.cmake
/usr/lib64/cmake/gflags/gflags-config.cmake
/usr/lib64/cmake/gflags/gflags-export-noconfig.cmake
/usr/lib64/cmake/gflags/gflags-export.cmake
/usr/lib64/libgflags.so
/usr/lib64/libgflags_nothreads.so
/usr/share/doc/gflags-devel-2.1.1
/usr/share/doc/gflags-devel-2.1.1/designstyle.css
/usr/share/doc/gflags-devel-2.1.1/gflags.html
```

例子：cmake

```
$ repoquery --list cmake
/etc/rpm/macros.cmake
/usr/bin/ccmake
/usr/bin/cmake
/usr/bin/cpack
/usr/bin/ctest
/usr/lib64/cmake
/usr/share/aclocal/cmake.m4
/usr/share/cmake
/usr/share/cmake/Modules
/usr/share/cmake/Modules/AddFileDependencies.cmake
/usr/share/cmake/Modules/AutomocInfo.cmake.in
/usr/share/cmake/Modules/BasicConfigVersion-AnyNewerVersion.cmake.in
/usr/share/cmake/Modules/BasicConfigVersion-ExactVersion.cmake.in
/usr/share/cmake/Modules/BasicConfigVersion-SameMajorVersion.cmake.in
/usr/share/cmake/Modules/BundleUtilities.cmake
/usr/share/cmake/Modules/CMake.cmake
/usr/share/cmake/Modules/CMakeASM-ATTInformation.cmake
/usr/share/cmake/Modules/CMakeASMCompiler.cmake.in
/usr/share/cmake/Modules/CMakeASMInformation.cmake
/usr/share/cmake/Modules/CMakeASM_MASMInformation.cmake
/usr/share/cmake/Modules/CMakeASM_NASMInformation.cmake
/usr/share/cmake/Modules/CMakeAddFortranSubdirectory
/usr/share/cmake/Modules/CMakeAddFortranSubdirectory.cmake
/usr/share/cmake/Modules/CMakeAddFortranSubdirectory/build_mingw.cmake.in
/usr/share/cmake/Modules/CMakeAddFortranSubdirectory/config_mingw.cmake.in
/usr/share/cmake/Modules/CMakeAddNewLanguage.txt
/usr/share/cmake/Modules/CMakeBackwardCompatibilityC.cmake
/usr/share/cmake/Modules/CMakeBackwardCompatibilityCXX.cmake
....
```


### 包的一般目录结构

从上面看到，一个软件包一般会有这几个子文件夹：

```
bin/
include/
lib64/
share/
```

其中 lib64/ 文件夹，在一些其他的软件里，也有可能是 lib/，这些文件夹的内容会复制到根目录下的 `/usr` 下。

现在知道了 `yum install` 实际上就是复制了一些文件到指定目录，为什么这就是安装好了？

## 为什么把相关文件放到指定目录下就叫安装好了？

1. 对于普通的二进制可执行文件来说（如cmake），其安装的主要步骤就是把二进制可执行文件放到 `/usr/bin` 下。
   1. 原因：PATH 环境变量
   2. shell 会从 PATH 环境变量中寻找可执行文件。
   3. 默认的 PATH 环境变量包含了 `/usr/bin` 路径。
   4. 所以说，把二进制可执行文件放到 `/usr/bin` 下就是安装好了。

一个例子：

```
$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/test/.local/bin:/home/test/bin
```

2. 对于头文件来说（`include/`下的文件），其安装就是把头文件放到 `/usr/include` 
   1. 原因：gcc 等编译器会默认找 `/usr/include` 路径下的头文件。
   2. [可通过该命令确认](https://stackoverflow.com/questions/4980819/what-are-the-gcc-default-include-directories) ： `gcc -xc -E -v -`

一个例子：

```
$ gcc -xc -E -v -
Using built-in specs.
COLLECT_GCC=gcc
Target: x86_64-redhat-linux
Configured with: ../configure --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --with-linker-hash-style=gnu --enable-languages=c,c++,objc,obj-c++,java,fortran,ada,go,lto --enable-plugin --enable-initfini-array --disable-libgcj --with-isl=/builddir/build/BUILD/gcc-4.8.5-20150702/obj-x86_64-redhat-linux/isl-install --with-cloog=/builddir/build/BUILD/gcc-4.8.5-20150702/obj-x86_64-redhat-linux/cloog-install --enable-gnu-indirect-function --with-tune=generic --with-arch_32=x86-64 --build=x86_64-redhat-linux
Thread model: posix
gcc version 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC) 
COLLECT_GCC_OPTIONS='-E' '-v' '-mtune=generic' '-march=x86-64'
 /usr/libexec/gcc/x86_64-redhat-linux/4.8.5/cc1 -E -quiet -v - -mtune=generic -march=x86-64
ignoring nonexistent directory "/usr/lib/gcc/x86_64-redhat-linux/4.8.5/include-fixed"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../x86_64-redhat-linux/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/lib/gcc/x86_64-redhat-linux/4.8.5/include
 /usr/local/include
 /usr/include
End of search list.
```

3. 对于库文件(lib)来说，gcc等编译器会默认找 `/usr/lib` 和 路径 `/usr/lib64`
   1. [参考](https://transang.me/library-path-in-gcc/)
   2. 可见命令 `ld --verbose | grep SEARCH_DIR | tr -s ' ;' \\012` 和 `gcc -print-search-dirs`

一个例子

```

$ ld --verbose | grep SEARCH_DIR | tr -s ' ;' \\012
SEARCH_DIR("=/usr/x86_64-redhat-linux/lib64")
SEARCH_DIR("=/usr/lib64")
SEARCH_DIR("=/usr/local/lib64")
SEARCH_DIR("=/lib64")
SEARCH_DIR("=/usr/x86_64-redhat-linux/lib")
SEARCH_DIR("=/usr/local/lib")
SEARCH_DIR("=/lib")
SEARCH_DIR("=/usr/lib")

$ gcc -print-search-dirs
install: /usr/lib/gcc/x86_64-redhat-linux/4.8.5/
programs: =/usr/libexec/gcc/x86_64-redhat-linux/4.8.5/:/usr/libexec/gcc/x86_64-redhat-linux/4.8.5/:/usr/libexec/gcc/x86_64-redhat-linux/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/:/usr/lib/gcc/x86_64-redhat-linux/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../x86_64-redhat-linux/bin/x86_64-redhat-linux/4.8.5/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../x86_64-redhat-linux/bin/
libraries: =/usr/lib/gcc/x86_64-redhat-linux/4.8.5/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../x86_64-redhat-linux/lib/x86_64-redhat-linux/4.8.5/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../x86_64-redhat-linux/lib/../lib64/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../x86_64-redhat-linux/4.8.5/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../lib64/:/lib/x86_64-redhat-linux/4.8.5/:/lib/../lib64/:/usr/lib/x86_64-redhat-linux/4.8.5/:/usr/lib/../lib64/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../x86_64-redhat-linux/lib/:/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../:/lib/:/usr/lib/
```

4. 在运行时，Linux 会默认从 `/lib64` 和 `/lib` 下找动态库，而 `/lib64` 会软链接到`/usr/lib64`
   1. [ldconfig](https://man7.org/linux/man-pages/man8/ldconfig.8.html)
   2. [一个参考](https://www.cnblogs.com/sddai/p/10397510.html)

一个例子：

```
$ ldconfig -v
ldconfig: Can't stat /libx32: No such file or directory
ldconfig: Path `/usr/lib' given more than once
ldconfig: Path `/usr/lib64' given more than once
ldconfig: Can't stat /usr/libx32: No such file or directory
/usr/lib64//bind9-export:
......
/usr/lib64/dyninst:
......
/usr/lib64/mysql:
......
/lib:
/lib64:
......
	libgflags_nothreads.so.2.1 -> libgflags_nothreads.so.2.1
	libgflags.so.2.1 -> libgflags.so.2.1
......
```

## 一个编译任务：编译带 gflags 依赖的 hello

如果没有安装 gflags：

```
$ g++ -o hello  hello.cpp
hello.cpp:2:27: fatal error: gflags/gflags.h: No such file or directory
 #include <gflags/gflags.h>
                           ^
compilation terminated.
make: *** [hello] Error 1
```

如果安装了 gflags，如 `yum install gflags-devel`

```
$ g++ -o hello  hello.cpp
/tmp/cc484Y8F.o: In function `main':
hello.cpp:(.text+0x24): undefined reference to `gflags::ParseCommandLineFlags(int*, char***, bool)'
/tmp/cc484Y8F.o: In function `__static_initialization_and_destruction_0(int, int)':
hello.cpp:(.text+0x130): undefined reference to `gflags::FlagRegisterer::FlagRegisterer(char const*, char const*, char const*, char const*, void*, void*)'
collect2: error: ld returned 1 exit status
```

再链上库

```
$ g++ -o hello  hello.cpp -lgflags
$ ./hello 
hello
test argument is : none
$ ./hello --test="hello workd"
hello
test argument is : hello workd
```

还可以使用 `ldd` 命令查看 `hello` 依赖的动态库。

```
$ ldd ./hello
	linux-vdso.so.1 =>  (0x00007fff2e7da000)
	libgflags.so.2.1 => /lib64/libgflags.so.2.1 (0x00007f7ed011f000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f7ecfe17000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f7ecfb15000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f7ecf8ff000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f7ecf531000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f7ecf315000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7ed0340000)

```

> 问题：如果只执行了`yum install gflags`，没有安装 `gflags-devel` ，会怎样？

## 非 root 手动安装

假设现在你没有 root ，没法执行 `yum install gflags-devel` 你要如何处理？？

我们可以手动编译 gflags， 安装到自定义路径中，自行处理依赖，不使用默认路径的特性。非 root 手动安装，意味着不能安装到根目录下，因此在默认路径下才有的一些特性，需要自己补上（如告诉gcc哪里找头文件，运行时哪里找动态库）

### 编译安装 gflags

下面是我的执行命令，需要注意的有以下几点：

1. 一定需要手动设置安装路径，cmake需要使用  `-DCMAKE_INSTALL_PREFIX=/home/test/software/install/gflags` 参数，路径可自定义。
2. 需要较高版本的cmake，可以通过`yum install cmake3` 来解决，也可以手动编译安装。

```shell
# 注意：源码安装的gflags版本比yum安装的要新，namespace 从 gflags 变成了 google ，要相应地改代码（见代码注释）
# 下载源码
# wget https://github.com/gflags/gflags/archive/refs/tags/v2.2.2.tar.gz
# 这个是国内加速链接
wget https://github.91chi.fun//https://github.com//gflags/gflags/archive/refs/tags/v2.2.2.tar.gz
# 解压
tar xf v2.2.2.tar.gz
cd gflags-2.2.2/
mkdir build
cd build
# 默认静态库
# cmake3 -DCMAKE_INSTALL_PREFIX=/home/test/software/install/gflags  ..
# 我打算使用动态库
cmake3 -DCMAKE_INSTALL_PREFIX=/home/test/software/install/gflags -DBUILD_SHARED_LIBS=on ..
make -j
make install
```

这是我的执行日志。

```
$ cmake3 -DCMAKE_INSTALL_PREFIX=/home/test/software/install/gflags -DBUILD_SHARED_LIBS=on  .. 
-- The CXX compiler identification is GNU 4.8.5
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Looking for C++ include unistd.h
-- Looking for C++ include unistd.h - found
-- Looking for C++ include stdint.h
-- Looking for C++ include stdint.h - found
-- Looking for C++ include inttypes.h
-- Looking for C++ include inttypes.h - found
-- Looking for C++ include sys/types.h
-- Looking for C++ include sys/types.h - found
-- Looking for C++ include sys/stat.h
-- Looking for C++ include sys/stat.h - found
-- Looking for C++ include fnmatch.h
-- Looking for C++ include fnmatch.h - found
-- Looking for C++ include stddef.h
-- Looking for C++ include stddef.h - found
-- Check size of uint32_t
-- Check size of uint32_t - done
-- Looking for strtoll
-- Looking for strtoll - found
-- Looking for C++ include pthread.h
-- Looking for C++ include pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE  
-- Check size of pthread_rwlock_t
-- Check size of pthread_rwlock_t - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/test/software/gflags-2.2.2/build

$ make -j
Scanning dependencies of target gflags_shared
Scanning dependencies of target gflags_nothreads_shared
[ 12%] Building CXX object CMakeFiles/gflags_nothreads_shared.dir/src/gflags.cc.o
[ 25%] Building CXX object CMakeFiles/gflags_shared.dir/src/gflags.cc.o
[ 37%] Building CXX object CMakeFiles/gflags_nothreads_shared.dir/src/gflags_reporting.cc.o
[ 62%] Building CXX object CMakeFiles/gflags_nothreads_shared.dir/src/gflags_completions.cc.o
[ 62%] Building CXX object CMakeFiles/gflags_shared.dir/src/gflags_reporting.cc.o
[ 75%] Building CXX object CMakeFiles/gflags_shared.dir/src/gflags_completions.cc.o
[ 87%] Linking CXX shared library lib/libgflags.so
[100%] Linking CXX shared library lib/libgflags_nothreads.so
[ 50%] Built target gflags_shared
[100%] Built target gflags_nothreads_shared
[100%] Built target gflags_shared
[100%] Built target gflags_nothreads_shared
$ make install
Install the project...
-- Install configuration: "Release"
-- Installing: /home/test/software/install/gflags/lib/libgflags.so.2.2.2
-- Up-to-date: /home/test/software/install/gflags/lib/libgflags.so.2.2
-- Up-to-date: /home/test/software/install/gflags/lib/libgflags.so
-- Installing: /home/test/software/install/gflags/lib/libgflags_nothreads.so.2.2.2
-- Up-to-date: /home/test/software/install/gflags/lib/libgflags_nothreads.so.2.2
-- Up-to-date: /home/test/software/install/gflags/lib/libgflags_nothreads.so
-- Installing: /home/test/software/install/gflags/include/gflags/gflags.h
-- Installing: /home/test/software/install/gflags/include/gflags/gflags_declare.h
-- Installing: /home/test/software/install/gflags/include/gflags/gflags_completions.h
-- Installing: /home/test/software/install/gflags/include/gflags/gflags_gflags.h
-- Installing: /home/test/software/install/gflags/lib/cmake/gflags/gflags-config.cmake
-- Installing: /home/test/software/install/gflags/lib/cmake/gflags/gflags-config-version.cmake
-- Installing: /home/test/software/install/gflags/lib/cmake/gflags/gflags-targets.cmake
-- Installing: /home/test/software/install/gflags/lib/cmake/gflags/gflags-targets-release.cmake
-- Installing: /home/test/software/install/gflags/lib/cmake/gflags/gflags-nonamespace-targets.cmake
-- Installing: /home/test/software/install/gflags/lib/cmake/gflags/gflags-nonamespace-targets-release.cmake
-- Up-to-date: /home/test/software/install/gflags/bin/gflags_completions.sh
-- Installing: /home/test/software/install/gflags/lib/pkgconfig/gflags.pc
-- Installing: /home/test/.cmake/packages/gflags/794537a04e66fd0f52ec9253ede021ed

```

这是我的安装结果：

```
$ tree /home/test/software/install/gflags
/home/test/software/install/gflags
├── bin
│   └── gflags_completions.sh
├── include
│   └── gflags
│       ├── gflags_completions.h
│       ├── gflags_declare.h
│       ├── gflags_gflags.h
│       └── gflags.h
└── lib
    ├── cmake
    │   └── gflags
    │       ├── gflags-config.cmake
    │       ├── gflags-config-version.cmake
    │       ├── gflags-nonamespace-targets.cmake
    │       ├── gflags-nonamespace-targets-release.cmake
    │       ├── gflags-targets.cmake
    │       └── gflags-targets-release.cmake
    ├── libgflags_nothreads.so -> libgflags_nothreads.so.2.2
    ├── libgflags_nothreads.so.2.2 -> libgflags_nothreads.so.2.2.2
    ├── libgflags_nothreads.so.2.2.2
    ├── libgflags.so -> libgflags.so.2.2
    ├── libgflags.so.2.2 -> libgflags.so.2.2.2
    ├── libgflags.so.2.2.2
    └── pkgconfig
        └── gflags.pc

7 directories, 18 files
```

安装好了之后，怎么用我安装好的 gflags ？

### 使用自己安装的 gflags （方法一：编译器选项）

我们尝试卸载用 `yum install gflags-devel` 安装的 gflags，执行编译，会报错。

```
$ g++ -o hello  hello.cpp
hello.cpp:2:27: fatal error: gflags/gflags.h: No such file or directory
 #include <gflags/gflags.h>
                           ^
compilation terminated.

```

找不到头文件，那我可以使用`-I` 选项，告诉编译器，在哪里找头文件：

```
$ ls /home/test/software/install/gflags/include/gflags/
gflags_completions.h  gflags_declare.h  gflags_gflags.h  gflags.h

$ g++ -o hello  hello.cpp -I/home/test/software/install/gflags/include
/tmp/ccTjiFnl.o: In function `main':
hello.cpp:(.text+0x24): undefined reference to `google::ParseCommandLineFlags(int*, char***, bool)'
/tmp/ccTjiFnl.o: In function `__static_initialization_and_destruction_0(int, int)':
hello.cpp:(.text+0x12d): undefined reference to `google::FlagRegisterer::FlagRegisterer<std::string>(char const*, char const*, char const*, std::string*, std::string*)'
collect2: error: ld returned 1 exit status

```

找不到函数定义，那肯定是没有链接库，再编译。

```
$ g++ -o hello  hello.cpp -I/home/test/software/install/gflags/include -lgflags
/usr/bin/ld: cannot find -lgflags
collect2: error: ld returned 1 exit status
```

但由于不在默认路径，找不到，我们需要`-L` 选项告诉编译器在哪找库

```shell
g++ -o hello  hello.cpp -I/home/test/software/install/gflags/include -lgflags -L /home/test/software/install/gflags/lib/

$ g++ -o hello  hello.cpp -I/home/test/software/install/gflags/include -lgflags -L /home/test/software/install/gflags/lib/
/home/test/software/install/gflags/lib//libgflags.a(gflags.cc.o): In function `google::(anonymous namespace)::FlagRegistry::GlobalRegistry()':
gflags.cc:(.text+0x10a6): undefined reference to `pthread_rwlock_wrlock'
gflags.cc:(.text+0x10c6): undefined reference to `pthread_rwlock_unlock'
gflags.cc:(.text+0x1167): undefined reference to `pthread_rwlock_init'
gflags.cc:(.text+0x1180): undefined reference to `pthread_rwlock_init'
/home/test/software/install/gflags/lib//libgflags.a(gflags.cc.o): In function `google::(anonymous namespace)::RegisterCommandLineFlag(char const*, char const*, char const*, google::(anonymous namespace)::FlagValue*, google::(anonymous namespace)::FlagValue*)':
gflags.cc:(.text+0x1426): undefined reference to `pthread_rwlock_wrlock'
gflags.cc:(.text+0x1446): undefined reference to `pthread_rwlock_unlock'
/home/test/software/install/gflags/lib//libgflags.a(gflags.cc.o): In function `google::(anonymous namespace)::AddFlagValidator(void const*, bool (*)())':
gflags.cc:(.text+0x1644): undefined reference to `pthread_rwlock_unlock'
gflags.cc:(.text+0x165c): undefined reference to `pthread_rwlock_wrlock'
/home/test/software/install/gflags/lib//libgflags.a(gflags.cc.o): In function `google::GetCommandLineOption(char const*, std::string*)':
gflags.cc:(.text+0x20ac): undefined reference to `pthread_rwlock_wrlock'
gflags.cc:(.text+0x20c4): undefined reference to `pthread_rwlock_unlock'
/home/test/software/install/gflags/lib//libgflags.a(gflags.cc.o): In function `google::GetCommandLineFlagInfo(char const*, google::CommandLineFlagInfo*)':

```

看来新版的gflags需要多一个依赖：pthread，因此再链接多一个pthread库


```
$ g++ -o hello  hello.cpp -I/home/test/software/install/gflags/include -lgflags -L /home/test/software/install/gflags/lib/ -lpthread
$ ./hello 
./hello: error while loading shared libraries: libgflags.so.2.2: cannot open shared object file: No such file or directory
$ ldd ./hello
	linux-vdso.so.1 =>  (0x00007ffd1dba6000)
	libgflags.so.2.2 => not found
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f1100e76000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f1100b6e000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f110086c000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f1100656000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f1100288000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f1101092000)
$ readelf -d ./hello | grep RPATH
```

运行时找不到库的路径，因此我们还需要增加 `-Wl,-rpath -WL,${lib_path}`。
通过观察 `readelf -d ./hello` 的输出（对比前后的输出），我们发现这条编译指令的作用是将库的路径写到了elf可执行文件中的RPATH部分，这样就能在运行时知道在哪里找库。

> 关于 rpath，一些参考链接：[wiki](https://en.wikipedia.org/wiki/Rpath)，[blog](https://nehckl0.medium.com/creating-relocatable-linux-executables-by-setting-rpath-with-origin-45de573a2e98)

```
$ g++ -o hello  hello.cpp -I/home/test/software/install/gflags/include -lgflags -L /home/test/software/install/gflags/lib/ -lpthread -Wl,-rpath -Wl,/home/test/software/install/gflags/lib/
$ ./hello 
hello
test argument is : none
$ ldd ./hello
	linux-vdso.so.1 =>  (0x00007fffa2fe5000)
	libgflags.so.2.2 => /home/test/software/install/gflags/lib/libgflags.so.2.2 (0x00007fb84d1ea000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fb84cfce000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fb84ccc6000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fb84c9c4000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fb84c7ae000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fb84c3e0000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb84d40a000)
$ readelf -d ./hello | grep RPATH
 0x000000000000000f (RPATH)              Library rpath: [/home/test/software/install/gflags/lib/]
```

总结一下，我们的编译指令最终迭代成这样：

```
g++ -o hello  \
hello.cpp \
-I/home/test/software/install/gflags/include \
-lgflags -L/home/test/software/install/gflags/lib/ \
-lpthread \
-Wl,-rpath -Wl,/home/test/software/install/gflags/lib/
```

> 这些手动添加的编译指令，在一些编译框架中如 cmake 、configure 、bazel 等，有其他的指定方式。

### 使用自己安装的 gflags （方法二：环境变量）


全程使用环境变量来搞定 （CPATH/LIBRARY_PATH/LD_LIBRARY_PATH）

`-I` : CPATH

`-L` : LIBRARY_PATH

`-Wl,-rpath -Wl ${lib_path}` : LD_LIBRARY_PATH

我们回到原来的编译指令，不设置任何环境变量，一步步查看这些环境变量的作用。

```
$ g++ -o hello hello.cpp -lgflags
hello.cpp:2:27: fatal error: gflags/gflags.h: No such file or directory
 #include <gflags/gflags.h>
                           ^
compilation terminated.
$ tree /home/test/software/install/gflags/include
/home/test/software/install/gflags/include
└── gflags
    ├── gflags_completions.h
    ├── gflags_declare.h
    ├── gflags_gflags.h
    └── gflags.h

1 directory, 4 files
```

首先是 `${CPATH}`，

```
$ export CPATH=/home/test/software/install/gflags/include:${CPATH}
$ g++ -o hello hello.cpp -lgflags
/usr/bin/ld: cannot find -lgflags
collect2: error: ld returned 1 exit status
$ tree /home/test/software/install/gflags/lib
/home/test/software/install/gflags/lib
├── cmake
│   └── gflags
│       ├── gflags-config.cmake
│       ├── gflags-config-version.cmake
│       ├── gflags-nonamespace-targets.cmake
│       ├── gflags-nonamespace-targets-release.cmake
│       ├── gflags-targets.cmake
│       └── gflags-targets-release.cmake
├── libgflags_nothreads.so -> libgflags_nothreads.so.2.2
├── libgflags_nothreads.so.2.2 -> libgflags_nothreads.so.2.2.2
├── libgflags_nothreads.so.2.2.2
├── libgflags.so -> libgflags.so.2.2
├── libgflags.so.2.2 -> libgflags.so.2.2.2
├── libgflags.so.2.2.2
└── pkgconfig
    └── gflags.pc

3 directories, 13 files
```


然后是 `${LIBRARY_PAHT}`，

```
$ export LIBRARY_PATH=/home/test/software/install/gflags/lib/:${LIBRARY_PATH}
$ g++ -o hello hello.cpp -lgflags
$ ./hello 
./hello: error while loading shared libraries: libgflags.so.2.2: cannot open shared object file: No such file or directory
$ ldd ./hello
	linux-vdso.so.1 =>  (0x00007ffd8f5f6000)
	libgflags.so.2.2 => not found
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fc9ebedc000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fc9ebbda000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fc9eb9c4000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc9eb5f6000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc9ec1e4000)
```

最后是 `${LD_LIBRARY_PAHT}`，

```
$ export LD_LIBRARY_PATH=/home/test/software/install/gflags/lib/:${LD_LIBRARY_PATH}
$ ./hello 
hello
test argument is : none
$ ldd ./hello
	linux-vdso.so.1 =>  (0x00007ffccbaeb000)
	libgflags.so.2.2 => /home/test/software/install/gflags/lib/libgflags.so.2.2 (0x00007ff392ed9000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007ff392bd1000)
	libm.so.6 => /lib64/libm.so.6 (0x00007ff3928cf000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007ff3926b9000)
	libc.so.6 => /lib64/libc.so.6 (0x00007ff3922eb000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007ff3920cf000)
	/lib64/ld-linux-x86-64.so.2 (0x00007ff3930f9000)
```


我们可以写一个脚本：`load_gflags.sh` 来完成环境变量的设置。

```
#!/bin/bash
function add_package_by_path {
        MY_SOFTWARE_PATH=$1
        export PATH=${MY_SOFTWARE_PATH}/bin:${PATH}
        export CPATH=${MY_SOFTWARE_PATH}/include:${CPATH}
        export LIBRARY_PATH=${MY_SOFTWARE_PATH}/lib:${LIBRARY_PATH}
        export LD_LIBRARY_PATH=${MY_SOFTWARE_PATH}/lib:${LD_LIBRARY_PATH}
        export LIBRARY_PATH=${MY_SOFTWARE_PATH}/lib64:${LIBRARY_PATH}
        export LD_LIBRARY_PATH=${MY_SOFTWARE_PATH}/lib64:${LD_LIBRARY_PATH}
}

add_package_by_path /home/test/software/install/gflags

```

可以看到，当我们执行 `source load_gflags.sh` 时，就能够使用 gflags 这个依赖了。

```
$ g++ -o hello hello.cpp -lgflags
hello.cpp:2:27: fatal error: gflags/gflags.h: No such file or directory
 #include <gflags/gflags.h>
                           ^
compilation terminated.
$ source load_gflags.sh 
$ g++ -o hello hello.cpp -lgflags
$ ./hello 
hello
test argument is : none
$ ./hello  --test="hello"
hello
test argument is : hello
$ 
```



## 相关学习材料

1. 《程序员的自我修养：链接、装载与库》 [中文版下载链接](https://pan.nscc-gz.cn/f/32f772ceb0004bf69f52/)
2. 编译一个文件的细节：[link](https://liuhaohua.com/server-programming-guide/basics/procedure/)
3. [ipads对cmake的讲解](https://www.bilibili.com/video/BV14h41187FZ)
4. [spack](https://github.com/spack/spack)，一个很好用的软件包管理器。
5. [nix](https://github.com/NixOS/nixpkgs)，可以尝试的软件包管理器