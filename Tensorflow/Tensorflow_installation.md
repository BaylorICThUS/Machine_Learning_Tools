# Tensorflow Installation

## Dependencies

### Yum update

### GCC
  * Require GCC-4.8 or 4.9
  * Make sure the GCC is compiled with the following configuration:
  ```
    ./configure --prefix=<prefix location> --enable-bootstrap --enable-shared --enable-threads=posix
  --enable-checking=release --with-system-zlib --enable-multilib --enable-__cxa_atexit --disable-libunwind-exceptions
  --enable-gnu-unique-object --enable-languages=c,c++,fortran --with-ppl --with-cloog --with-tune=generic
  --with-arch_32=i686 --build=x86_64-redhat-linux
  ```

### Binutils
  * Use newly installed GCC to compile & install the latest binutils (latest now is 2.26, I used 2.25 worked which
    worked fine, the minimum requirement is 2.22 or 2.24, I don't remember).

### Python
  * Install Latest python2 (2.7.12) and python3 (3.5.2)
  * For python2 use the following configuration options at least:
  ```
  ./configure --prefix=<prefix location> --enable-shared --enable-unicode=ucs4
  ```
  * For python3 use the following configuration options at least:
  ```
  ./configure --prefix=<prefix location> --enable-shared
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
  git checkout 0.3.0
  ```

===========

## Installation
