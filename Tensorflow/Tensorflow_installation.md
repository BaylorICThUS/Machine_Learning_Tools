# Tensorflow Installation

## Dependencies

### Yum update

### GCC
  * Require GCC-4.8 or 4.9
  * Make sure the GCC is compiled with the following configuration:

  ```
  $ ./configure --prefix=<prefix location> --enable-bootstrap --enable-shared --enable-threads=posix
  --enable-checking=release --with-system-zlib --enable-multilib --enable-__cxa_atexit --disable-libunwind-exceptions
  --enable-gnu-unique-object --enable-languages=c,c++,fortran --with-ppl --with-cloog --with-tune=generic
  --with-arch_32=i686 --build=x86_64-redhat-linux
  ```

### Binutils
  * Use newly installed GCC to compile & install the latest binutils (latest now is 2.26, I used 2.25 which
    worked fine, the minimum requirement is 2.22 or 2.24, I don't remember).

### Python
  * Install Latest python2 (2.7.12) and python3 (3.5.2)
  * For python2 use the following configuration options at least:

  ```
  $ ./configure --prefix=<prefix location> --enable-shared --enable-unicode=ucs4
  ```

  * For python3 use the following configuration options at least:

  ```
  $ ./configure --prefix=<prefix location> --enable-shared
  ```

  * Also, it's a good idea to specify ```--libdir=```, so that all major versions are using the same libdir which is
  where "site-packages" is located. Otherwise, when upgrade, say from 2.7.12 to 2.7.14, or from 3.5.2 to 3.5.5, you
  might need to install tensorflow again.
  
### Java
  * Latest JDK from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

### Bazel (Optional, I have binary avaiable)
  * Clone the [Bazel repository](https://github.com/bazelbuild/bazel)
  * Go to the repository and checkout latest release version (0.3.0 at the moment):

  ```
  $ git checkout 0.3.0
  ```
 
  * Patch file: third_party/protobuf/BUILD

    @line 88:

    ```
    -LINK_OPTS = ["-lpthread"]
    +LINK_OPTS = ["-lpthread", "-lrt", "-lm"]
    ```
    
  * Patch file: tools/cpp/CROSSTOOL
    From line 72 to 199 (toolchain "local_linux"), update all the gcc, g++ path, as well as all the include directory
    and binutils' binarys' paths, in my case it looks like this (path might differ):
    
    ```diff
    diff --git a/tools/cpp/CROSSTOOL b/tools/cpp/CROSSTOOL
    index 5a23b58..9a6c24a 100644
    --- a/tools/cpp/CROSSTOOL
    +++ b/tools/cpp/CROSSTOOL
    @@ -88,34 +88,39 @@ toolchain {
       target_system_name: "local"
       toolchain_identifier: "local_linux"
    
    -  tool_path { name: "ar" path: "/usr/bin/ar" }
    -  tool_path { name: "compat-ld" path: "/usr/bin/ld" }
    
    
    -  tool_path { name: "cpp" path: "/usr/bin/cpp" }
    +  tool_path { name: "ar" path: "/usr/local/GCC/4.8.5/bin/ar" }
    +  tool_path { name: "compat-ld" path: "/usr/local/GCC/4.8.5/bin/ld" }
    +  tool_path { name: "cpp" path: "/usr/local/GCC/4.8.5/bin/cpp" }
       tool_path { name: "dwp" path: "/usr/bin/dwp" }
    -  tool_path { name: "gcc" path: "/usr/bin/gcc" }
    +  tool_path { name: "gcc" path: "/usr/local/GCC/4.8.5/bin/gcc" }
       cxx_flag: "-std=c++0x"
    +  cxx_flag: "-std=c++11"
    +  cxx_flag: "-std=gnu++11"
       linker_flag: "-lstdc++"
    -  linker_flag: "-B/usr/bin/"
    +  linker_flag: "-B/usr/local/GCC/4.8.5/bin/"
    
       # TODO(bazel-team): In theory, the path here ought to exactly match the path
       # used by gcc. That works because bazel currently doesn't track files at
       # absolute locations and has no remote execution, yet. However, this will need
       # to be fixed, maybe with auto-detection?
    -  cxx_builtin_include_directory: "/usr/lib/gcc/"
    -  cxx_builtin_include_directory: "/usr/local/include"
    -  cxx_builtin_include_directory: "/usr/include"
    -  tool_path { name: "gcov" path: "/usr/bin/gcov" }
    +  # cxx_builtin_include_directory: "/usr/lib/gcc/"
    +  cxx_builtin_include_directory: "/usr/local/GCC/4.8.5/lib/gcc/"
    +  cxx_builtin_include_directory: "/usr/local/GCC/4.8.5/include/"
    +  cxx_builtin_include_directory: "/usr/local/GCC/4.8.5/include/c++/4.8.5/"
    +  cxx_builtin_include_directory: "/usr/local/include/"
    +  cxx_builtin_include_directory: "/usr/include/"
    +  tool_path { name: "gcov" path: "/usr/local/GCC/4.8.5/bin/gcov" }
    
       # C(++) compiles invoke the compiler (as that is the one knowing where
       # to find libraries), but we provide LD so other rules can invoke the linker.
    -  tool_path { name: "ld" path: "/usr/bin/ld" }
    +  tool_path { name: "ld" path: "/usr/local/GCC/4.8.5/bin/ld" }
    
    -  tool_path { name: "nm" path: "/usr/bin/nm" }
    -  tool_path { name: "objcopy" path: "/usr/bin/objcopy" }
    +  tool_path { name: "nm" path: "/usr/local/GCC/4.8.5/bin/nm" }
    +  tool_path { name: "objcopy" path: "/usr/local/GCC/4.8.5/bin/objcopy" }
       objcopy_embed_flag: "-I"
       objcopy_embed_flag: "binary"
    -  tool_path { name: "objdump" path: "/usr/bin/objdump" }
    -  tool_path { name: "strip" path: "/usr/bin/strip" }
    +  tool_path { name: "objdump" path: "/usr/local/GCC/4.8.5/bin/objdump" }
    +  tool_path { name: "strip" path: "/usr/local/GCC/4.8.5/bin/strip" }
       # Anticipated future default.
  
       unfiltered_cxx_flag: "-no-canonical-prefixes"
    ```
    
  * Compile bazel:
  
  ```
  $ CC=<full path to gcc> CXX=<full path to c++> ./compile.sh
  ```
  
  * Note:
    1. There needs to be enough space in /tmp, otherwise it will fail.
    2. It's better to compile as root, since the compilation will spawn a lot of threads which normal user does not have
       enough resource to run (assuming the limit -u is 1024, unless it's increased to 2000)


====================================================================================================

## Tensorflow Installation

  * Clone the [tensorflow repository](https://github.com/tensorflow/tensorflow.git)
  * Go to the repository and checkout latest release version (v0.9.0 at the moment):

  ```
  $ git checkout v0.9.0
  ```
  
  * Patch file: third_party/gpus/crosstool/CROSSTOOL
    From line 22 to 154, update the CPP binary path, include directory path, and binutils' binaries' paths (No need to
    update GCC). Here is what my patched file looks like (Note: path might differ):
    
    
    ```diff
    diff --git a/third_party/gpus/crosstool/CROSSTOOL b/third_party/gpus/crosstool/CROSSTOOL
    index 8db81a9..b35c43c 100644
    --- a/third_party/gpus/crosstool/CROSSTOOL
    +++ b/third_party/gpus/crosstool/CROSSTOOL
    @@ -38,9 +38,9 @@ toolchain {
       target_system_name: "local"
       toolchain_identifier: "local_linux"
    
    -  tool_path { name: "ar" path: "/usr/bin/ar" }
    -  tool_path { name: "compat-ld" path: "/usr/bin/ld" }
    -  tool_path { name: "cpp" path: "/usr/bin/cpp" }
    +  tool_path { name: "ar" path: "/usr/local/GCC/4.8.5/bin/ar" }
    +  tool_path { name: "compat-ld" path: "/usr/local/GCC/4.8.5/bin/ld" }
    +  tool_path { name: "cpp" path: "/usr/local/GCC/4.8.5/bin/cpp" }
       tool_path { name: "dwp" path: "/usr/bin/dwp" }
       # As part of the TensorFlow release, we place some cuda-related compilation
       # files in third_party/gpus/crosstool/clang/bin, and this relative
    @@ -51,27 +51,29 @@ toolchain {
       # and the device compiler to use "-std=c++11".
       cxx_flag: "-std=c++11"
       linker_flag: "-lstdc++"
    -  linker_flag: "-B/usr/bin/"
    +  linker_flag: "-B/usr/local/GCC/4.8.5/bin/"
    
       # TODO(bazel-team): In theory, the path here ought to exactly match the path
       # used by gcc. That works because bazel currently doesn't track files at
       # absolute locations and has no remote execution, yet. However, this will need
       # to be fixed, maybe with auto-detection?
    -  cxx_builtin_include_directory: "/usr/lib/gcc/"
    +  cxx_builtin_include_directory: "/usr/local/GCC/4.8.5/lib/gcc/"
    +  cxx_builtin_include_directory: "/usr/local/GCC/4.8.5/include/"
    +  cxx_builtin_include_directory: "/usr/local/GCC/4.8.5/include/c++/4.8.5/"
       cxx_builtin_include_directory: "/usr/local/include"
       cxx_builtin_include_directory: "/usr/include"
    -  tool_path { name: "gcov" path: "/usr/bin/gcov" }
    +  tool_path { name: "gcov" path: "/usr/local/GCC/4.8.5/bin/gcov" }
    
       # C(++) compiles invoke the compiler (as that is the one knowing where
       # to find libraries), but we provide LD so other rules can invoke the linker.
       tool_path { name: "ld" path: "/usr/bin/ld" }
    
    -  tool_path { name: "nm" path: "/usr/bin/nm" }
    -  tool_path { name: "objcopy" path: "/usr/bin/objcopy" }
    +  tool_path { name: "nm" path: "/usr/local/GCC/4.8.5/bin/nm" }
    +  tool_path { name: "objcopy" path: "/usr/local/GCC/4.8.5/bin/objcopy" }
       objcopy_embed_flag: "-I"
       objcopy_embed_flag: "binary"
    -  tool_path { name: "objdump" path: "/usr/bin/objdump" }
    -  tool_path { name: "strip" path: "/usr/bin/strip" }
    +  tool_path { name: "objdump" path: "/usr/local/GCC/4.8.5/bin/objdump" }
    +  tool_path { name: "strip" path: "/usr/local/GCC/4.8.5/bin/strip" }
    
       # Anticipated future default.
       unfiltered_cxx_flag: "-no-canonical-prefixes"
    ```

  * Python package:
    - Install pip for both python2 and python3, and use their pip to install numpy, wheel, ipython, jupyter
    
  * Install CUDA 7.5
  
  * Install latest cuDNN
  
  * Go to tensorflow directory and run: ```./configure```
    - You might need to install and specify SWIG_PATH, depending on where it's installed.
    - This and following steps might need to be repeated for different python version

  * Build gpu support (make sure there is enough space in /tmp, need more than 2.0GB):
  
  ```
  $ /path/to/bazel build -c opt --config=cuda //tensorflow/cc:tutorials_example_trainer
  ```

  * Test gpu support:

  ```
  $ bazel-bin/tensorflow/cc/tutorials_example_trainer --use_gpu
  ```
  
  * Build pip packages with GPU support:
  
  ```
  $ /path/to/bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
  $ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
  ```

  Python2
  
  ```
  $ sudo pip install /tmp/tensorflow_pkg/tensorflow-0.9.0-py2-none-any.whl
  ```

  Python3

  ```
  $ sudo pip3 install /tmp/tensorflow_pkg/tensorflow-0.9.0-py3-none-any.whl
  ```

  * Test:

  ```
  $ cd tensorflow/models/image/mnist
  $ python convolutional.py
  ```

