# Litecoin


## Build Litecoin Client
```shell
$ mkdir -p $HOME/xu/root
$ git clone https://github.com/litecoin-project/litecoin.git
$ cd litecoin
$ git checkout v0.14.2
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
```

Here are the binaries built and installed under prefix
```shell
$ ls -lrt
total 548520
-rwxr-xr-x 1 loki loki 104744648 Ara 26 16:06 litecoind
-rwxr-xr-x 1 loki loki   7646480 Ara 26 16:06 litecoin-cli
-rwxr-xr-x 1 loki loki  16907856 Ara 26 16:06 litecoin-tx
-rwxr-xr-x 1 loki loki 148646104 Ara 26 16:06 litecoin-qt
```

Run Litecoin client on Testnet with custom data directory
```
$ mkdir -p $HOME/xu/litecoin
$ $HOME/xu/root/bin/litecoin-qt -datadir=$HOME/xu/litecoin -testnet
```

Litecoin Testnet Faucet
```
http://testnet.thrasher.io
```

Litecoin Testnet Explorer
```
https://chain.so/testnet/ltc
```
