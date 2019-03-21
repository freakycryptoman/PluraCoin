The PluraCoin (PLURA) is a anonymous cryptocurrency and privacy centric with opaque and more analysis resistant blockchain.

## Building PluraCoin 

### On *nix (Ubuntu 18.04 LTS reccommended)

Dependencies: GCC 4.7.3 or later, CMake 2.8.6 or later, and Boost 1.55.

You may download them from:

* http://gcc.gnu.org/
* http://www.cmake.org/
* http://www.boost.org/

* Alternatively, it may be possible to install them using a package manager:

````
sudo apt-get install build-essential cmake libboost-all-dev -y
````

Ubuntu 18.04 LTS already comes with Git preinstalled. To check if you really have Git installed type:

````
git --version
````

You should get response like 

````
git version 2.7.4
````

If Git is not found then install it:

````
sudo apt-get install git -y
````

Download existing blockchain to speed up initial synchronization from 
https://blockchain.pluracoin.org/blockchain.zip
and unzip it to folder .pluracoin
````
cd ~
mkdir .pluracoin
cd .pluracoin
wget https://blockchain.pluracoin.org/blockchain.zip
unzip blockchain.zip
rm blockchain.zip

````

Now clone the PluraCoin with Git to your home directory (or wherever you want):

````
cd ~
git clone https://github.com/pluracoin/PluraCoin.git
````

To build PluraCoin, change to a directory where this file is located, and run `make`.

````
cd PluraCoin
make
````

The resulting executables can be found in `build/release/src`.

**Advanced options:**

* Parallel build: run `make -j <number of threads>` instead of `make`. You can safely run `make -j 4` on VPS with two cores and 4 GB RAM.
* Debug build: run `make build-debug`.
* Test suite: run `make test-release` to run tests in addition to building. Running `make test-debug` will do the same to the debug version.
* Building with Clang: it may be possible to use Clang instead of GCC, but this may not work everywhere. To build, run `export CC=clang CXX=clang++` before running `make`.


Start daemon 

````
cd build/release/src
./pluracoind
````

Wait for blockchain sync and you're done !

More info about daemon, wallet and simplewallet can be found in Wiki https://github.com/pluracoin/PluraCoin/wiki

---

### On Windows
Dependencies: MSVC 2013 or later, CMake 2.8.6 or later, and Boost 1.55. You may download them from:

* http://www.microsoft.com/
* http://www.cmake.org/
* http://www.boost.org/

To build, change to a directory where this file is located, and run theas commands: 
```
mkdir build
cd build
cmake -G "Visual Studio 12 Win64" ..
```

And then do Build.
Good luck!

---

### Building for Android on Linux

Set up the 32 bit toolchain
Download and extract the Android SDK and NDK
```
android-ndk-r15c/build/tools/make_standalone_toolchain.py --api 21 --stl=libc++ --arch arm --install-dir /opt/android/tool32
```

Download and setup the Boost 1.65.1 source
```
wget https://sourceforge.net/projects/boost/files/boost/1.65.1/boost_1_65_1.tar.bz2/download -O boost_1_65_1.tar.bz2
tar xjf boost_1_65_1.tar.bz2
cd boost_1_65_1
./bootstrap.sh
```
apply patch from external/boost1_65_1/libs/filesystem/src

Build Boost with the 32 bit toolchain
```
export PATH=/opt/android/tool32/arm-linux-androideabi/bin:/opt/android/tool32/bin:$PATH
./b2 abi=aapcs architecture=arm binary-format=elf address-model=32 link=static runtime-link=static --with-chrono --with-date_time --with-filesystem --with-program_options --with-regex --with-serialization --with-system --with-thread --with-context --with-coroutine --with-atomic --build-dir=android32 --stagedir=android32 toolset=clang threading=multi threadapi=pthread target-os=android --reconfigure stage
```

Build PluraCoin for 32 bit Android
```
mkdir -p build/release.android32
cd build/release.android32
CC=clang CXX=clang++ cmake -D BUILD_TESTS=OFF -D ARCH="armv7-a" -ldl -D STATIC=ON -D BUILD_64=OFF -D CMAKE_BUILD_TYPE=release -D ANDROID=true -D BUILD_TAG="android" -D BOOST_ROOT=/opt/android/boost_1_65_1 -D BOOST_LIBRARYDIR=/opt/android/boost_1_65_1/android32/lib -D CMAKE_POSITION_INDEPENDENT_CODE:BOOL=true -D BOOST_IGNORE_SYSTEM_PATHS_DEFAULT=ON ../..
make SimpleWallet
```
