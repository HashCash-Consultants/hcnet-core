# HCNet Core

[![HashCash Consultants](https://hr.hashcashconsultants.com/assets/images/hashcash-logo-mail.png)](https://www.hashcashconsultants.com/)

HC-net is a open source with a [public repository](https://github.com/HashCash-Consultants/hcnet-core)

Installation Instructions
==================
These are instructions for building hcnet-core from source.

For a potentially quicker set up, the following projects could be good alternatives:

* hcnet-core in a [docker container](https://github.com/hcnet/docker-hcnet-core)
* hcnet-core and [horizon](https://github.com/hcnet/go/tree/master/services/horizon) in a [docker container](https://github.com/hcnet/docker-hcnet-core-horizon)
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


### OS X
When building on OSX, here's some dependencies you'll need:
- Install xcode
- Install [homebrew](https://brew.sh)
- `brew install libsodium`
- `brew install libtool`
- `brew install autoconf`
- `brew install automake`
- `brew install pkg-config`
- `brew install libpq` (required for postgres)
- `brew install openssl` (required for postgres)
- `brew install parallel` (required for running tests)
- `brew install ccache` (required for enabling ccache)

You'll also need to configure pkg-config by adding the following to your shell (`.zshenv` or `.zshrc`):
```zsh
export PKG_CONFIG_PATH="$PKG_CONFIG_PATH:$(brew --prefix)/opt/libpq/lib/pkgconfig"
export PKG_CONFIG_PATH="$PKG_CONFIG_PATH:$(brew --prefix)/opt/openssl@3/lib/pkgconfig"
```


## Basic Installation

- `git clone https://github.com/HashCash-Consultants/hcnet-core.git --branch v18.0.0`
- `cd hcnet-core`
- `git submodule init`
- `git submodule update`
- Type `./autogen.sh`.
- Type `./configure`   *(If configure complains about compiler versions, try `CXX=clang-10 ./configure` or `CXX=g++-8 ./configure` or similar, depending on your compiler.)*
- Type `make` or `make -j<N>` (where `<N>` is the number of parallel builds, a number less than the number of CPU cores available, e.g. `make -j3`)
- Type `make check` to run tests.
- Type `make install` to install.


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
    git clone https://github.com/hcnet/hcnet-core.git
    cd hcnet-core/
    ./autogen.sh && ./configure && make -j6

## Building with Tracing

Configuring with `--enable-tracy` will build and embed the client component of the [Tracy](https://github.com/wolfpld/tracy) high-resolution tracing system in the `hcnet-core` binary.

The tracing client will activate automatically when hcnet-core is running, and will listen for connections from Tracy servers (a command-line capture utility, or a cross-platform GUI).

The Tracy server components can also be compiled by configuring with `--enable-tracy-gui` or `--enable-tracy-capture`.

The GUI depends on the `capstone`, `freetype` and `glfw` libraries and their headers, and on linux or BSD the `GTK-2.0` libraries and headers. On Windows and MacOS, native toolkits are used instead.


    # On Ubuntu
    $ sudo apt-get install libcapstone-dev libfreetype6-dev libglfw3-dev libgtk2.0-dev

    # On MacOS
    $ brew install capstone freetype2 glfw

    
## Building with Rust

Configuring with `--enable-next-protocol-version-unsafe-for-production` will build and embed components written in the [Rust](https://rust-lang.org) programming language. These components are currently only enabled when building the "next" protocol, not the "current" one.

Building the Rust components requires the `cargo` package manager and build system, as well as the `rustc` compiler, both version 1.57 or later.
Currently we recommend using the system packages provided on Ubuntu, and the rust project's `rustup` installer on other systems.

    # On Ubuntu
    $ sudo apt-get install cargo

    # On MacOS
    $ brew install rustup-init
    $ rustup-init

To use an IDE with `rust-analyzer`, additional packages are required on Ubuntu:

    # On Ubuntu
    $ sudo apt-get install rust-src
