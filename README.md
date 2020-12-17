# HCNet Core

[![HashCash Consultants](https://github.com/HashCash-Consultants/HCNet-Core/raw/master/img/hashcash-logo.png)](https://www.hashcashconsultants.com/)

HC-net is a open source with a [public repository](https://github.com/HashCash-Consultants/HCNet-Core)

# Build Dependencies

- c++ toolchain and headers that supports c++14
    - `clang` >= 5.0
    - `g++` >= 6.0
- `pkg-config`
- `bison` and `flex`
- `libpq-dev` unless you `./configure --disable-postgres` in the build step below.
- 64-bit system
- `clang-format-5.0` (for `make format` to work)
- `pandoc`
- `perl`

### Ubuntu

#### Ubuntu 14.04
You will have to install the [test toolchain](#adding-the-test-toolchain) in order to both build and run HCNet-Core.

#### Ubuntu 16.04
Just like 14.04, you can install the test toolchain to build and run HCNet-Core.

Alternatively, if you want to just depend on stock 16.04, you will have to build with clang *and* have use `libc++` instead of `libstdc++` when compiling.

After installing packages, head to [building with clang and libc++](#building-with-clang-and-libc).

# install clang-8 toolchain
sudo apt-get install clang-8

Adding the test toolchain
# NOTE: newer version of the compilers are not
#    provided by stock distributions
#    and are provided by the /test toolchain
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update

Installing packages
# common packages
sudo apt-get install git build-essential pkg-config autoconf automake libtool bison flex libpq-dev libunwind-dev parallel
# if using clang
sudo apt-get install clang-5.0
# clang with libstdc++
sudo apt-get install gcc-6
# if using g++ or building with libstdc++
# sudo apt-get install gcc-6 g++-6 cpp-6

Basic Installation
git clone https://github.com/HashCash-Consultants/HCNet-Core
cd hcnet-core
git submodule init
git submodule update
Type ./autogen.sh.
Type ./configure (If configure complains about compiler versions, try CXX=clang-5.0 ./configure or CXX=g++-6 ./configure or similar, depending on your compiler.)
Type make or make -j<N> (where <N> is the number of parallel builds, a number less than the number of CPU cores available, e.g. make -j3)
Type make check to run tests.
Type make install to install.

# install libc++ headers
sudo apt-get install libc++-8-dev libc++abi-8-dev
Here are sample steps to achieve this:

export CC=clang-8
export CXX=clang++-8
export CFLAGS="-O3 -g1 -fno-omit-frame-pointer"
export CXXFLAGS="$CFLAGS -stdlib=libc++"
git clone https://github.com/HashCash-Consultants/HCNet-Core
cd hcnet-core/
./autogen.sh && ./configure && make -j6

Postgres database need to install to store core data
## Install postgres database
```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```
- Postgres user for HCNet core
```
sudo -s
su – postgres
createuser <username> --pwprompt
Enter password for new role: <Enter password>
Enter it again: <Enter the pwd again>
```
Note: This is required for DB url that need to be maintained in HcNet-core.cfg and if you want to set up 3 nodes, you need to create 3 users.
- After creating the user, you need to add them. So exit from postgres and login as root user.
```
exit
adduser <username>;
```
To verify if user is created, execute following commands
```
su - postgres
psql
\du
```
After that create a database. If you have five cores, then create five databases.
```
CREATE DATABASE <DB_NAME> OWNER <user created username>;
```