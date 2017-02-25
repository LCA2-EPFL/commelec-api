## Cross compilation of commelecd daemon for 32-bit Windows on x86 machines

The steps below are tested on 64-bit Ubuntu 16.04 LTS 
but should work on other linux machines too.

Cross compiling the commelecd daemon for 32-bit Windows consists of a number of steps
* Building mxe 
* Building commelec-api dependencies
* Cross-Building the commelecd daemon

## Building mxe

Follow the [mxe](http://mxe.cc/) installation instructions available at this [tutorial](http://mxe.cc/#tutorial) for setting up mxe cross build environment.

First, you need to install [mxe requirements](http://mxe.cc/#requirements), i.e., specific versions of unix tools.
This depends on your operating system but for ubuntu, the installation procedure is the same as [debian](http://mxe.cc/#requirements-debian).
More specifically, if you've a 64-bit debian (ubuntu) machine running Debian Jessie (8) or Ubuntu Utopic (14.10) or later, run the following commmands:

```
apt-get install \
    autoconf automake autopoint bash bison bzip2 flex gettext\
    git g++ gperf intltool libffi-dev libgdk-pixbuf2.0-dev \
    libtool libltdl-dev libssl-dev libxml-parser-perl make \
    openssl p7zip-full patch perl pkg-config python ruby scons \
    sed unzip wget xz-utils
apt-get install g++-multilib libc6-dev-i386
apt-get install libtool-bin
```
Once the requirements are installed, download the current version of mxe somewhere in your home directory.
```
git clone https://github.com/mxe/mxe.git
```
Build some basic tools (this takes some time; you can switch the context for a little while or have a break!)
```
cd mxe
make gcc
```
Edit your .bashrc script in order to change $PATH:
```
export PATH=/where MXE is installed/usr/bin:$PATH
```


## Building commelec-api dependencies

### Boost libraries
Boost libraries are available as mxe package. So it is easy to install them. Just do the following in mxe source directory.
```
make boost
```

### Eigen3 library
Again, eigen3 is available as mxe package. So just do the following in mxe source directory.
```
make eigen
```

### Capnproto library
Capnproto library is not available as mxe package. So you need to cross-compile it using mxe toolchain.
For cross-compiling Capnproto, you must have capnproto already installed on your system.
For that, please follow the [capnproto installation instructions](https://capnproto.org/install.html).
More specifically, you do the following:
```
curl -O https://capnproto.org/capnproto-c++-0.5.3.tar.gz
tar zxf capnproto-c++-0.5.3.tar.gz
cd capnproto-c++-0.5.3
./configure
make -j6 check
sudo make install
```

Once you've the capnproto installed on your system, you can go ahead cross-compiling it for windows. Please keep in mind that you must have the same version of capnproto installed on your system that you're cross-compiling.
Following commands will cross-compile capnproto for 32-bit windows and install it in mxe toolchain:
```
curl -O https://capnproto.org/capnproto-c++-0.5.3.tar.gz
tar zxf capnproto-c++-0.5.3.tar.gz
cd capnproto-c++-0.5.3
./configure --with-external-capnp --host=i686-w64-mingw32.static --enable-static --disable-shared --disable-reflection --prefix=/where mxe is installed/usr/i686-w64-mingw32.static
make -j6 check
sudo make install
```
It is important to note that we only cross-compile **light** version of capnproto for windows. This is done using --disable-reflection in configure options. 


## Cross-Building the commelecd daemon

First we clone the project:
```
git clone --recursive https://github.com/LCA2-EPFL/commelec-api.git
```
Create build environment
```
cd commelec-api
mkdir _windows_build
cd _windows_build
i686-w64-mingw32.static-cmake ..
```
At this step, CMake will complain if it cannot find the libraries.
If everthing goes well, you can build the commelecd daemon
```
make commelecd
```
