# ABY 3

<!-- [![Build Status](https://travis-ci.org/osu-crypto/libOTe.svg?branch=master)](https://travis-ci.org/osu-crypto/libOTe) -->

 
## Introduction
 
This library provides the semi-honest implementation of ABY 3.

## Install
 
The library is *cross platform* and has been tested on Windows and Linux. The dependencies are:

 * [libOTe](https://github.com/osu-crypto/libOTe)
 * [Boost](http://www.boost.org/) (networking)
 * [function2](https://github.com/Naios/function2)
 * [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page)
 
### Windows

In `Powershell`, this will set up the project

```
git clone --recursive https://github.com/osu-crypto/libOTe.git
cd libOTe/cryptoTools/thirdparty/win
getBoost.ps1 
cd ../../..
libOTe.sln
```

To see all the command line options, execute the program 

`frontend.exe` 

**Boost and visual studio 2017:**  If boost does not build with visual studio 2017 
follow [these instructions](https://stackoverflow.com/questions/41464356/build-boost-with-msvc-14-1-vs2017-rc). 

**Enabling Relic (for fast base OTs):**
 * Clone the Visual Studio port [Relic](https://github.com/ladnir/relic) `git clone --branch new_master https://github.com/ladnir/relic.git`. 
 * Use the CMake command  `cmake . -DMULTI=OPENMP -DCMAKE_INSTALL_PREFIX:PATH=C:\libs  -DCMAKE_GENERATOR_PLATFORM=x64` generate a Visual Studio solution
 * Optional: Build with gmp/mpir for faster performance. 
 * Install it to `C:\libs` (build the `INSTALL` VS project).
 * Edit the config file [libOTe/cryptoTools/cryptoTools/Common/config.h](https://github.com/ladnir/cryptoTools/blob/master/cryptoTools/Common/config.h) to include `#define ENABLE_RELIC`.

**Enabling Miracl (for base OTs):**
 * `cd libOTe/cryptoTools/thirdparty/win`
 * `getMiracl.ps1 ` (If the Miracl script fails to find visual studio 2017,  manually open and build the Miracl solution.)
 * `cd ../../..`
 * Edit the config file [libOTe/cryptoTools/cryptoTools/Common/config.h](https://github.com/ladnir/cryptoTools/blob/master/cryptoTools/Common/config.h) to include `#define ENABLE_MIRACL`.

**IMPORTANT:** By default, the build system needs the NASM compiler to be located
at `C:\NASM\nasm.exe`. In the event that it isn't, there are two options, install it, 
or enable the pure c++ implementation:
 * Remove  `cryptoTools/Crypto/asm/sha_win64.asm` from the cryptoTools Project.
 * Edit the config file [libOTe/cryptoTools/cryptoTools/Common/config.h](https://github.com/ladnir/cryptoTools/blob/master/cryptoTools/Common/config.h) to remove `#define ENABLE_NASM`.



### Linux / Mac
 
 In short, this will build the project (without base OTs)

```
git clone --recursive https://github.com/osu-crypto/libOTe.git
cd libOTe/cryptoTools/thirdparty/linux
bash boost.get
cd ../../..
cmake .
make
```

This will build the minimum version of the library (wihtout base OTs). The libraries 
will be placed in `libOTe/lib` and the binary `frontend_libOTe` will be placed in 
`libOTe/bin` To see all the command line options, execute the program 
 
`./bin/frontend_libOTe`


**Enable Base OTs using:**
 * `cmake .  -DENABLE_MIRACL=ON`: Build the library with integration to the
     [Miracl](https://www.miracl.com/index) library. Requires building miracl 
 `   cd libOTe/cryptoTools/thirdparty/linux; bash miracl.get`.

 * `cmake .  -DENABLE_RELIC=ON`: Build the library with integration to the 
      [Relic](https://github.com/relic-toolkit/relic/) library. Requires that
      relic is built with `cmake . -DMULTI=OPENMP` and installed.
 * **Linux Only**: `cmake .  -DENABLE_SIMPLESTOT=ON`: Build the library with integration to the 
      [SimplestOT](https://github.com/osu-crypto/libOTe/tree/master/SimplestOT) 
       library implementing a base OT. Also works with only relic but is slower.

**Other Options:**
 * `cmake .  -DENABLE_CIRCUITS=ON`: Build the library with the circuit library enabled.
 * `cmake .  -DENABLE_NASM=ON`: Build the library with the assembly base SHA1 implementation. Requires the NASM compiler.
 


**Note:** In the case that miracl or boost is already installed, the steps 
`cd libOTe/cryptoTools/thirdparty/linux; bash boost.get` can be skipped and CMake will attempt 
to find them instead. Boost is found with the CMake findBoost package and miracl
is found with the `find_library(miracl)` command.
 


### Linking

 You can either `make install` on linux or link libOTe's source tree. In the latter 
 case, you will need to include the following:

1) .../libOTe
2) .../libOTe/cryptoTools
3) .../libOTe/cryptoTools/thirdparty/linux/boost
4) .../libOTe/cryptoTools/thirdparty/linux/miracl/miracl <i>(if enabled)</i>
5) [Relic includes] <i>(if enabled)</i>

and link:
1) .../libOTe/bin/liblibOTe.a
2) .../libOTe/bin/libcryptoTools.a
3) .../libOTe/bin/libSimplestOT.a    <i>(if enabled)</i>
4) .../libOTe/cryptoTools/thirdparty/linux/boost/stage/lib/libboost_system.a
5) .../libOTe/cryptoTools/thirdparty/linux/boost/stage/lib/libboost_thread.a
6) .../libOTe/cryptoTools/thirdparty/linux/miracl/miracl/source/libmiracl.a <i>(if enabled)</i>
7) [Relic binar] <i>(if enabled)</i>


**Note:** On windows the linking paths follow a similar pattern.

## Help
 
Contact Peter Rindal rindalp@oregonstate.edu for any assistance on building 
or running the library.

## Citing

 Spread the word!

```
@misc{libOTe,
    author = {Peter Rindal},
    title = {{libOTe: an efficient, portable, and easy to use Oblivious Transfer Library}},
    howpublished = {\url{https://github.com/osu-crypto/libOTe}},
}
```

## Protocol Details
The 1-out-of-N [OOS16] protocol currently is set to work forn N=2<sup>76</sup>
but is capable of supporting arbitrary codes given the generator matrix in text 
format. See `./libOTe/Tools/Bch511.txt` for an example.
 
The 1-out-of-N  [KKRT16] for arbitrary N is also implemented and slightly faster
than [OOS16]. However, [KKRT16] is in the semi-honest setting.
 
The approximate K-out-of-N OT [RR16] protocol is also implemented. This protocol 
allows for a rough bound on the value K with a very light weight cut and choose 
technique. It was introduced for a PSI protocol that builds on a Garbled Bloom Filter.
 
 
\* Delta-OT does not use the RandomOracle or AES hash function.
 
\** This timing was taken from the [[OOS16]](http://eprint.iacr.org/2016/933) paper 
and their implementation used multiple threads. The number was not specified. When 
using the libOTe implementation with multiple threads, a timing of 2.6 seconds was 
obtained with the RandomOracle hash function.
 
It should be noted that the libOTe implementation uses the Boost ASIO library to 
perform more efficient asynchronous network IO. This involves using a background 
thread to help process network data. As such, this is not a completely fair comparison 
to the Apricot implementation but we don't expect it to have a large impact. It also 
appears that the Encrypto Group implementation uses asynchronous network IO.
 

 The above timings were obtained with the follwoing options:

 1-out-of-2 malicious:
 * Apricot: `./ot.x -n 16777216 -p 0 -m a -l 100 & ./ot.x -p 1 -m a -n 16777216 -l 100`
 * Encrypto Group: ` ./ot.exe -r 0 -n 16777216 -o 1 &  ./ot.exe -r 1 -n 16777216 -o 1`
 * emp-toolkit:  2x 2<sup>23</sup> `./mot 0 1212 & ./mot 1 1212`

1-out-of-2 semi-honest:
 * Apricot:  `./ot.x -n 16777216 -p 0 -m a -l 100 -pas & ./ot.x -p 1 -m a -n 16777216 -l 100 -pas`
 * Encrypto Group: ` ./ot.exe -r 0 -n 16777216 -o 0 &  ./ot.exe -r 1 -n 16777216 -o 0`
 * emp-toolkit:  2 * 2<sup>23</sup> `./shot 0 1212 & ./shot 1 1212`

  
  
 
 ## License
 
This project has been placed in the public domain. As such, you are unrestricted in how 
you use it, commercial or otherwise. However, no warranty of fitness is provided. If you 
found this project helpful, feel free to spread the word and cite us.
 
 

## Citation
 
[IKNP03] - Yuval Ishai and Joe Kilian and Kobbi Nissim and Erez Petrank, _Extending Oblivious Transfers Efficiently_. 
 
[KOS15]  - Marcel Keller and Emmanuela Orsini and Peter Scholl, _Actively Secure OT Extension with Optimal Overhead_. [eprint/2015/546](https://eprint.iacr.org/2015/546)
 
[OOS16]  - Michele Orrù and Emmanuela Orsini and Peter Scholl, _Actively Secure 1-out-of-N OT Extension with Application to Private Set Intersection_. [eprint/2016/933](http://eprint.iacr.org/2016/933)
 
[KKRT16]  - Vladimir Kolesnikov and Ranjit Kumaresan and Mike Rosulek and Ni Trieu, _Efficient Batched Oblivious PRF with Applications to Private Set Intersection_. [eprint/2016/799](https://eprint.iacr.org/2016/799)
 
[RR16]  - Peter Rindal and Mike Rosulek, _Improved Private Set Intersection against Malicious Adversaries_. [eprint/2016/746](https://eprint.iacr.org/2016/746)

[BLNNOOSS15]  - Sai Sheshank Burra and Enrique Larraia and Jesper Buus Nielsen and Peter Sebastian Nordholt and Claudio Orlandi and Emmanuela Orsini and Peter Scholl and Nigel P. Smart, _High Performance Multi-Party Computation for Binary Circuits Based on Oblivious Transfer_. [eprint/2015/472](https://eprint.iacr.org/2015/472.pdf)

[ALSZ15]  - Gilad Asharov and Yehuda Lindell and Thomas Schneider and Michael Zohner, _More Efficient Oblivious Transfer Extensions with Security for Malicious Adversaries_. [eprint/2015/061](https://eprint.iacr.org/2015/061)
 
[NP01]  -    Moni Naor, Benny Pinkas, _Efficient Oblivious Transfer Protocols_. 

