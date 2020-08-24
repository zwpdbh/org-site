+++
title = "C and C++ Compile"
date = 2020-08-24T10:00:00+08:00
lastmod = 2020-08-24T10:03:57+08:00
tags = ["Tools", "c", "cpp"]
draft = false
foo = "bar"
baz = "zoo"
alpha = 1
beta = "two words"
gamma = 10
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- [Refs](#refs)
- [GCC](#gcc)
    - [Compile a C program](#compile-a-c-program)
    - [Setting search path](#setting-search-path)
    - [Extended search paths](#extended-search-paths)
    - [Shared libraries and static libraries](#shared-libraries-and-static-libraries)
- [Cmake](#cmake)
    - [My example (A big project contains sub-projects)](#my-example--a-big-project-contains-sub-projects)
    - [Set CMake build variables](#set-cmake-build-variables)

</div>
<!--endtoc-->


## Refs {#refs}


## GCC {#gcc}


### Compile a C program {#compile-a-c-program}

-   compile multiple files

    ```sh
    gcc -Wall main.c hello_fn.c -o newhello
    ```
-   separate compile and link

    ```sh
    gcc -Wall -c main.c
    gcc -Wall -c hello_fn.c
    gcc main.o hello_fn.o -o newhello
    ```

    -   the benifit of it is that, if there one file is changed, only need to compile that file, and then link with other object files, much faster
-   Link with external libraries (static)
    -   Libraries are typically stored in special archive files with the extension `.a`, referred to as static libraries. They are created from object files with a sperate tool, the GNU archiver `ar`, and used by the linker to resolve references to functions at compile-time.
    -   In general, the compiler option `-lNAME` will attempt to link object files with a library file `libNAME.a` in the standard library directiories (additional directories can specified with commoand-line options and environment variables)
    -   For example, a program `data.c` using the GNU Linear Programming library `libglpk.a`, which in turn uses the math library `libm.a`, should be compiled as:

        ```sh
        $gcc -Wall data.c -lglpk -lm
        ```
-   a commonly basic compile is:

    ```sh
    gcc -W -Wall -O2 -ansi -pedantic -g first-program.c -o print-nums
    ```

    -   `-W` `-Wall` tells gcc to check for the widest range of possible errors.
    -   `-O2` (that is O for optimize, not zero), specifies the optimization level.
    -   `-ansi` `-pedantic` ensure that anything other than official ANSI/ISO will be rejected.
    -   `-g` flag includes debugging information in the executable.
    -   `-o` flag comes before the name of executable file you want to produce.


### Setting search path {#setting-search-path}

-   A common problem when compiling a program using library header file is the error: `FILE.h: No such file or directory`. This occurs if a header file is not present in the standard include file directories used by gcc.
-   A similar problem can occur for libraries: `/user/bin/ld: cannot find library`. This happens if a library used for linking is not present in the standard library directories used by gcc.
-   The list of directiories for header files is often referred to as the `include path`.
-   The list of directories for libraries is often referred to as the `link path`.
-   Suppose the program needed to be compiled with header file `/opt/gdbm-1.8.3/include/gdbm.h` and lib file `/opt/gdbm-1.8.3/lib/libgdbm.a`. Then the command to compiler could be

    ```sh
    gcc -Wall -I/opt/gdbm-1.8.3/include -L/opt/gdbm-1.8.3/lib dbmain.c -lgdbm
    ```
-   For above example, if header file is at `/usr/local/include/gdbm.h` and the lib file is at `/usr/local/lib/libgdbm.a`, then the compile command should be

    ```sh
    gcc -Wall -I/usr/local/include -L/usr/local/lib \
    dbmain.c -lgdbm -o dbmain
    ```


### Extended search paths {#extended-search-paths}

-   Several directories can be specified together in an environment variable as a colon separated list:

    ```text
    DIR1:DIR2:DIR3:...
    export PATH="/usr/local/bin:$PATH"
    ```
-   When environment variables and command-line options are used together, the compiler do the search in the following order:
    -   command line optionals
    -   directories specified by environment virables
    -   default system directories


### Shared libraries and static libraries {#shared-libraries-and-static-libraries}

-   **static libraries** are the `.a` files. When a program is linked against a static library, the machine code from the object files for any external functions used by the program is copied from the library into the final executable
-   Shared libraries used the extension `.so` which stands for **shared object**. It make the executable file smaller.
-   An executable file linked against a shared library contains only a small table of the functions it requires, instead of the complete machine code from the object files for the external functions. Before the executable file starts running, the machine code for the external functions is copied into memory from the shared library file on disk by the operating systema process referred to as `dynamic linking`.
-   Except the shared library make executable files smaller, it also makes it possible to update a library without recompiling the programs which use it (provide the interface to the library does not change).
-   gcc compiles programs to use shared libraries by default. Whenever a static library `libNAME.a` would be used for linking with the option `-lNAME` the compiler first checks for an alternative shared library with the same name with a `.so` extension. The simplest way to set the load path through the environment variable `LD_LIBRARY_PATH`.
    -   For example, the following commands set the load path to `/opt/gdbm-1.8.3/lib` so that `libgdbm.so` can be found:

        ```sh
        LD_LIBRARY_PATH=/opt/gdbm-1.8.3/lib
        export LD_LIBRARY_PATH
        ```
-   If the load path contains existing entries, it can be extended using:

    ```text
    LD_LIBRARY_PATH=NEWDIRS:$LD_LIBRARY_PATH
    ```
-   Alternatively, static linking can be forced with the `-static` option to gcc to avoid the use of shared libraries


## Cmake {#cmake}


### My example (A big project contains sub-projects) {#my-example--a-big-project-contains-sub-projects}

-   In big project's CMakeLists.txt

    ```text
    cmake_minimum_required(VERSION3.8)
    project(Learning_Unix_Network_Programming)

    set(CMAKE_C_STANDARD99)

    #===forUNIXNetworkProgramming
    add_executable(
    Learning_Unix_network_programming_daytime
    daytime/daytime_client.clib/unp.hLearning_Unix_network_programming/config.hlib/unp.c)


    #===forLearning_TCP_IP_socket_in_C
    add_subdirectory(./Learning_TCP_IP_socket_in_C/ch02_basic_TCP_sockets)
    add_subdirectory(./Learning_TCP_IP_socket_in_C/ch03_names_and_address_families)
    #add_subdirectory(./Learning_TCP_IP_socket_in_C/ch04_using_UDP_sockets)
    add_subdirectory(./Learning_TCP_IP_socket_in_C/ch05_sending_and_receiving_data)
    add_subdirectory(./Learning_TCP_IP_socket_in_C/my_tools)


    #===forlearningconcurrentprogramminginC++
    add_subdirectory(./Learning_C++_Concurrency/playground)
    add_subdirectory(./Learning_C++_Concurrency/demo)


    #===forparallelcomputing
    add_subdirectory(./Parallel_Computing/learning_pthreads/01)
    add_subdirectory(./Parallel_Computing/learning_pthreads/02)
    add_subdirectory(./Parallel_Computing/learning_pthreads/03)
    ```

-   In one of the sub projects CMakeLists.txt

    ```text
    #setminimumversion
    cmake_minimum_required(VERSION3.9)

    #setprojectname
    project(pthreads_creating_and_terminating)

    set(CMAKE_C_COMPILER/usr/bin/gcc)
    set(CMAKE_CXX_COMPILER/usr/bin/g++)


    #settheoutputfolderwhereyourprogramwillbecreated
    set(CMAKE_BINARY_DIR${CMAKE_SOURCE_DIR}/bin)
    set(EXECUTABLE_OUTPUT_PATH${CMAKE_BINARY_DIR})
    set(LIBRARY_OUTPUT_PATH${CMAKE_BINARY_DIR})
    #CMAKE_SOURCE_DIRiswherecmakewasstarted,thetoplevelsourcedirectory
    #CMAKE_BINARY_DIRisthesameasCMAKE_SOURCE_DIR,otherwisethisisthetopleveldirectoryofyourbuildtree

    include_directories("${PROJECT_SOURCE_DIR}")
    #containsthefullpathtotherootofyourprojectsourcedirectory(forexample,tothenearestdirectorywhere
    #CMakeLists.txtcontainsthePROJECT()command)


    SET(GCC_HEADER_PATH"-I/usr/include")
    SET(GCC_SHARED_LIB_PATH"-L/usr/lib")
    SET(GCC_LINK_FLAGS"-lpthread")

    SET(CMAKE_CXX_FLAGS"${CMAKE_CXX_FLAGS}${GCC_HEADER_PATH}${GCC_SHARED_LIB_PATH}${GCC_LINK_FLAGS}")



    add_executable(
        simple_one
        simple_one.cpp
    )

    add_executable(
        multiple_arguments
        multiple_arguments.cpp
    )
    ```

-   In the folder which contains CMakeLists.txt

    ```sh
    cmake -H. -Bbuild
    cmake --build build -- -j4
    ```


### Set CMake build variables {#set-cmake-build-variables}

-   ref: [CMAKE\_C\_COMPILER and CMAKE\_CXX\_COMPILER?](https://stackoverflow.com/questions/11588855/how-do-you-set-cmake-c-compiler-and-cmake-cxx-compiler-for-building-assimp-for-i)
-   Option1
    -   You can set CMake variables at command line

        ```sh
        cmake -D CMAKE_C_COMPILER="/path/to/your/c/compiler/executable" -D CMAKE_CXX_COMPILER "/path/to/your/cpp/compiler/executable" /path/to/directory/containing/CMakeLists.txt
        ```
    -   See [this](http://www.cmake.org/cmake/help/v2.8.12/cmake.html#opt%3a-Dvar%3atypevalue) to learn how to create a CMake cache entry.
-   Option2
    -   You can set envrionment variables `CC` and `CXX` to point to your C and C++ compiler executable respectively, such as:

        ```sh
        export CC=/path/to/your/c/compiler/executable
        export CXX=/path/to/your/cpp/compiler/executable
        cmake /path/to/directory/containing/CMakeLists.txt
        ```
-   Option3
    -   Set command in CMake

        ```cmake
        set(CMAKE_C_COMPILER "/path/to/your/c/compiler/executable")
        set(CMAKE_CXX_COMPILER "/path/to/your/cpp/compiler/executable")
        ```

        -   **Must** be added before you use `project()` or `enable_language()` command.