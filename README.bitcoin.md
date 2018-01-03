# Bitcoin


## Install Bitcoin Dependencies
For Ubuntu/Debian systems install packages below following [Bitcoin requirements](https://github.com/bitcoin/bitcoin/blob/master/doc/build-unix.md#dependency-build-instructions-ubuntu--debian)  
Tested with Ubuntu 16.04 LTS 

#### for generic dependencies
```shell
$ sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils python3
```

#### for Boost packages
```shell
$ sudo apt-get install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev
```

#### for Ubuntu db4.8 package
```shell
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:bitcoin/bitcoin
$ sudo apt-get update
$ sudo apt-get install libdb4.8-dev libdb4.8++-dev
```

#### for zero message queue
```shell
$ sudo apt-get install libzmq3-dev
```

#### for Qt GUI
```
$ sudo apt-get install qtdeclarative5-dev
$ sudo apt-get install qttools5-dev-tools
```

#### for protobuf compiler
```
$ sudo apt-get install protobuf-compiler
```


## Build Bitcoin Client
```shell
$ mkdir -p $HOME/xu/root
$ git clone https://github.com/bitcoin/bitcoin.git
$ cd bitcoin
$ git checkout v0.15.1
$ ./autogen.sh
$ ./configure --prefix=$HOME/xu/root --enable-static --disable-shared --enable-wallet --without-libs --with-gui=qt5 --without-miniupnpc --without-qrencode --disable-tests --disable-bench
$ make
$ make install
```

Here are the final build options
```
Options used to compile and link:
  with wallet   = yes
  with gui / qt = yes
    qt version  = 5
    with qr     = no
  with zmq      = yes
  with test     = no
  with bench    = no
  with upnp     = no
  debug enabled = no
  werror        = no

  target os     = linux
  build os      = 

  CC            = gcc
  CFLAGS        = -g -O2
  CPPFLAGS      =  -DHAVE_BUILD_INFO -D__STDC_FORMAT_MACROS
  CXX           = g++ -std=c++11
  CXXFLAGS      = -g -O2 -Wall -Wextra -Wformat -Wvla -Wformat-security -Wno-unused-parameter
  LDFLAGS       = 
  ARFLAGS       = cr
```

Here are the binaries built and installed under prefix
```shell
$ ls -lrt root/bin/
total 277084
-rwxr-xr-x 1 loki loki 108967544 Ara 26 15:19 bitcoind
-rwxr-xr-x 1 loki loki   7993592 Ara 26 15:19 bitcoin-cli
-rwxr-xr-x 1 loki loki  16719456 Ara 26 15:19 bitcoin-tx
-rwxr-xr-x 1 loki loki 150048488 Ara 26 15:19 bitcoin-qt
```

Run Bitcoin client on Testnet with custom data directory
```
$ mkdir -p $HOME/xu/bitcoin
$ $HOME/xu/root/bin/bitcoin-qt -datadir=$HOME/xu/bitcoin -testnet
```

Bitcoin Testnet Faucet
```
https://testnet.manu.backend.hamburg/faucet
```

Bitcoin Testnet Explorer
```
https://www.blocktrail.com/tBTC
https://live.blockcypher.com/btc-testnet/
```