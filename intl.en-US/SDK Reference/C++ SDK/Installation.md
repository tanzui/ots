# Installation {#concept_31764_zh .concept}

C++ is special, so we recommend that you use the methods described in this topic to compile and copy `build/release/src/tablestore/core/impl/buildinfo.cpp` for backup. Then copy the C++ SDK source code and `buildinfo.cpp` to your own code library and compile it using your compilation system.

## Compilation parameters {#section_kb5_ljf_2fb .section}

When compiling the client-side code, some compiler behaviors must be ensured. This means that some compiler parameters are required.

The GCC compiler parameters are described as follows.

|Parameter|Required|Description|
|:--------|:-------|:----------|
|--std=gnu++03|Yes|Supports C++98 TR1 language with GCC extension \(that is, typeof\).|
|-pthread|Yes|Required parameter for multithreaded programming. This parameter is required for both compiling and linking.|
|-fwrapv|Yes|Rotate upon integer overflow. Specifically, this yields 0 in the case of an unsigned integer overflow and the smallest negative number in the case of a signed integer overflow. The client checks overflow based on this behavior.|
|-O2|Recommended|Optimization grade. In general, a higher optimization grade is not recommended.|
|-fsanitize=address and-fvar-tracking-assignments|Recommended|Later versions than gcc-4.9 support libasan, a lightweight detector that can quickly detect errors in memory usage. If you need Table Store developers to locate errors, please take these two compilation errors so that the error can be reproduced. You must also take the previous parameter when linking.**Note:** libasan and valgrind are incompatible.

|

## Environment dependency and pre-compiled packages { .section}

The Debian 8 system is used here as an example to demonstrate how to generate pre-compiled packages.

1.  Open the `docker/debian8/Dockerfile` file and select dockerfile to export the environment dependency of the client on the system. This method automatically guarantees the consistency between the code and the environment.

    ```
    RUN apt-get install -y scons g++ libboost-all-dev protobuf-compiler libprotobuf-dev uuid-dev libssl-dev
    RUN apt-get install -y ca-certificates # for HTTPS support
    
    ```

    The SDK depends on the following:

    -   scons & gcc: Compilation system
    -   boost
    -   uuid
    -   protobuf: serialization library
    -   openssl: signature, used to support HTTPS
    -   ca-certificates: used to support HTTPS only. If you only use the Table Store HTTP address, you can choose not to install this library. We recommend that you use the more secure HTTPS.
2.  You can compile the client after installing these packages. Download the source code of the client and run `scons` in the source code directory.

    ```
    $ git clone https://github.com/aliyun/aliyun-tablestore-cpp-sdk.git
    $ cd aliyun-tablestore-cpp-sdk
    $ scons -j4
    
    ```


Once these steps have been completed, a tar package is compiled. Typically, you can find the package name in the final output of `scons`. For example, the package name and path of the Debian 8 system are: `build/release/pkg/aliyun-tablestore-cpp98-sdk-4.4.1-debian8.9-x86_64.tar.gz`.

-   The package name includes the following elements:

    -   C++ version \(C++98\)
    -   SDK version \(4.4.1\)
    -   OS version \(Debian 8.9\)
    -   OS architecture \(x86\_64\)
-   The contents of the package include:

    ```
    $ tar -tf build/release/pkg/aliyun-tablestore-cpp98-sdk-4.4.1-debian8.9-x86_64.tar.gz
    version.ini
    lib/libtablestore_core.so
    lib/libtablestore_core_static.a
    lib/libtablestore_util.so
    lib/libtablestore_util_static.a
    include/tablestore/util/arithmetic.hpp
    include/tablestore/util/assert.hpp
    include/tablestore/util/foreach.hpp
    include/tablestore/util/iterator.hpp
    include/tablestore/util/logger.hpp
    include/tablestore/util/logging.hpp
    include/tablestore/util/mempiece.hpp
    include/tablestore/util/mempool.hpp
    include/tablestore/util/metaprogramming.hpp
    include/tablestore/util/move.hpp
    include/tablestore/util/optional.hpp
    include/tablestore/util/prettyprint.hpp
    include/tablestore/util/random.hpp
    include/tablestore/util/result.hpp
    include/tablestore/util/security.hpp
    include/tablestore/util/seq_gen.hpp
    include/tablestore/util/threading.hpp
    include/tablestore/util/timestamp.hpp
    include/tablestore/util/try.hpp
    include/tablestore/util/assert.ipp
    include/tablestore/util/iterator.ipp
    include/tablestore/util/logging.ipp
    include/tablestore/util/mempiece.ipp
    include/tablestore/util/move.ipp
    include/tablestore/util/prettyprint.ipp
    include/tablestore/core/client.hpp
    include/tablestore/core/error.hpp
    include/tablestore/core/range_iterator.hpp
    include/tablestore/core/retry.hpp
    include/tablestore/core/types.hpp
    
    ```

    The package currently includes the following elements:

    -   Version file: `version.ini` 
    -   Library files: all files under `lib/`. Where `libtablestore_core_static.a` depends on `libtablestore_util_static.a`. The Moments library is similar.
    -   Header files: all files under `include/`.

