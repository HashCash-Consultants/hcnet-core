# HCNet Core

[![HashCash Consultants](https://hr.hashcashconsultants.com/assets/images/hashcash-logo-mail.png)](https://www.hashcashconsultants.com/)

HC-net is a open source with a [public repository](https://github.com/HashCash-Consultants/hcnet-core)

Installation Instructions
==================
These are instructions for building hcnet-core from source.

For a potentially quicker set up, the following projects could be good alternatives:


* hcnet-core and [horizon](https://github.com/hcnet/go/tree/master/services/horizon) 
* pre-compiled [packages](https://github.com/hcnet/packages)

## Picking a version to run

Best is to use the latest *stable* release that can be downloaded from [release](https://github.com/HashCash-Consultants/hcnet-core/releases)


Alternatively, branches are organized in the following way:

| branch name | description | quality bar |
| ----------- | ----------- | ----------- |
| master      | version currently deployed on the live network | all unit tests passing |

For convenience, we also keep a record in the form of release tags of the
 versions that make it to production:
 * pre-releases are versions that get deployed to testnet
 * releases are versions that made it all the way to production

## Containerized dev environment

We maintain a pre-configured Docker configuration ready for development with VSCode.

See the [dev container's README](.devcontainer/README.md) for more detail.

## Runtime dependencies

`hcnet-core` does not have many dependencies.

If core was configured (see below) to work with Postgresql, a local Postgresql server
 will need to be deployed to the same host.

To install Postgresql, follow instructions from the [Postgresql download page](https://www.postgresql.org/download/).

## Build Dependencies

- c++ toolchain and headers that supports c++17
    - `clang` >= 10.0
    - `g++` >= 8.0
- `pkg-config`
- `bison` and `flex`
- `libpq-dev` unless you `./configure --disable-postgres` in the build step below.
- 64-bit system
- `clang-format-10` (for `make format` to work)
- `perl`
- `libunwind-dev`

### Ubuntu

#### Ubuntu 18.04
You can install the [test toolchain](#adding-the-test-toolchain) to build and run hcnet-core with the latest version of the llvm toolchain.

Alternatively, if you want to just depend on stock Ubuntu, you will have to build with clang *and* have use `libc++` instead of `libstdc++` when compiling.

Ubuntu 18.04 has clang-10 available, that you can install with

    # install clang-10 toolchain
    sudo apt-get install clang-10

After installing packages, head to [building with clang and libc++](#building-with-clang-and-libc).


#### Adding the test toolchain (optional)
    # NOTE: newer version of the compilers are not
    #    provided by stock distributions
    #    and are provided by the /test toolchain
    sudo apt-get install software-properties-common
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update

#### Installing packages
    # common packages
    sudo apt-get install git build-essential pkg-config autoconf automake libtool bison flex libpq-dev libunwind-dev parallel
    # if using clang
    sudo apt-get install clang-10
    # clang with libstdc++
    sudo apt-get install gcc-8
    # if using g++ or building with libstdc++
    # sudo apt-get install gcc-8 g++-8 cpp-8

In order to make changes, you'll need to install the proper version of clang-format.

In order to install the llvm (clang) toolchain, you may have to follow instructions on https://apt.llvm.org/

    sudo apt-get install clang-format-10


## Basic Installation

- `git clone https://github.com/HashCash-Consultants/hcnet-core.git --branch v19.3.0`


## Building with clang and libc++

On some systems, building with `libc++`, [LLVM's version of the standard library](https://libcxx.llvm.org/) can be done instead of `libstdc++` (typically used on Linux).

NB: there are newer versions available of both clang and libc++, you will have to use the versions suited for your system.

You may need to install additional packages for this, for example, on Linux Ubuntu 18.04 LTS with clang-10:

    # install libc++ headers
    sudo apt-get install libc++-10-dev libc++abi-10-dev

Here are sample steps to achieve this:

    export CC=clang-10
    export CXX=clang++-10
    export CFLAGS="-O3 -g1 -fno-omit-frame-pointer"
    export CXXFLAGS="$CFLAGS -stdlib=libc++"

    ./autogen.sh && ./configure && make -j6

Now your core build is completed 


Now you setup next step aurora.... 
## 🔗 Aurora Link
[![Aurora](https://img.shields.io/badge/Go-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://github.com/HashCash-Consultants/go.git/)
