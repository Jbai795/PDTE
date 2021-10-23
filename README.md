# Private Decision Tree Evaluation (PDTE) Protocols

By *Ágnes Kiss* ([ENCRYPTO](http://www.encrypto.de), TU Darmstadt), *Masoud Naderpour* (University of Helsinki), *Jian Liu* (University of California, Berkeley), *N. Asokan* (Aalto University) and *Thomas Schneider* ([ENCRYPTO](http://www.encrypto.de), TU Darmstadt) in [PoPETs'19(2)](https://petsymposium.org/2019/). Paper available [here](http://encrypto.de/papers/KNLAS19.pdf)

### Features
---

Our implementation for private decision tree evaluation protocols includes the protocols HHH, HGH, GGH, GGG and HGG (it can also be used to benchmark the last and most inefficient protocol HHG). The implementation of (SelH+)CompH and PathH is based on the mcl library (https://github.com/herumi/mcl) that implements efficient lifted ElGamal encryption over elliptic curves, more specifically on the XCMP protocol implementation which implements the DGK comparison protocol (https://github.com/fionser/XCMP). The implementation of SelH, SelG, CompG and PathG are based on the ABY framework (https://github.com/encryptogroup/ABY) which provides an efficient implementation of Yao's garbled circuit protocol as well as of the Paillier homomorphic encryption scheme.

This code is provided as a experimental implementation for testing purposes and should not be used in a productive environment. We cannot guarantee security and correctness.

### Requirements
---
The requirements are the same as that of https://github.com/fionser/XCMP and https://github.com/encryptogroup/ABY.

### PDTE Implementation
---

1. Clone a copy of the main PDTE git repository by running:
```
git clone --recursive git://github.com/encryptogroup/PDTE
```
2. Enter the UC directory: `cd PDTE`

#### (SelH+)CompH and PathH Implementation
3. Clone/download the XCMP repository (https://github.com/fionser/XCMP) in the PDTE folder: use git clone https://github.com/fionser/XCMP.git, do not add recursive or submodule (because that will submodule the old version of mcl)!
4. Enter XCMP :`cd XCMP`
5. Clone/download the mcl repository (https://github.com/herumi/mcl) in the XCMP folder: git clone https://github.com/fionser/XCMP.git. My used version is: e33ccfd94a1db776ec36493aa40e76159dc23a15
6. Place/replace the files from XCMP_files into the respective location in the XCMP folder
7. Enter mcl :`cd XCMP/mcl`
```
mkdir build
cd build
cmake ..
make
```

9. Add the following lines at the enf of XCMP/benchmark_gt/CMakeLists.txt:
```
add_executable(hhh hhh.cpp)
target_link_libraries(hhh boost_system pthread ${ECC_LIB})
```
10. Run the following commands:
```
cd benchmark_gt
mkdir build & cd build
cmake .. -DCMAKE_BUILD_TYPE=Release & make
```
11. In two separate terminals, run ```./hhh 0``` and ```./hhh 1``` for the server and client applications. You can configure the DT and PROT variables in the beginning of the file benchmark_dt/hhh.cpp for running different protocol parts and decision trees. 

#### SelG, SelH, CompG and PathG Implementation
1. Clone/download the ABY repository
2. Place the dectree folder from ABY_example in ABY/src/examples and add the line
```
add_subdirectory(dectree)
```
in ABY/src/examples/CMakeLists.txt.
3. Add the following line in ABY/src/abycore/sharing/yaoserversharing.h in line 74:
```
CBitVector get_R(){ return m_vR;}
```
4. Add the following lines in ABY/src/abycore/circuit/booleancircuits.h in line 474:
```
BYTE* GetEvaluatedKey(uint32_t gateid) { return m_vGates[gateid].gs.yval; };
BYTE* GetServerRandomKey(uint32_t gateid) {return m_vGates[gateid].gs.yinput.outKey; };
BYTE* GetPi(uint32_t gateid) {return m_vGates[gateid].gs.yinput.pi; };
```
5. Build ABY with the examples as indicated at https://github.com/encryptogroup/ABY.
```
cd ABY
mkdir build & cd build
cmake .. -DABY_BUILD_EXE=On
make
```
6. Runing two terminals in ABY/build/bin
```
./decision_tree_test -r 0
```
```
./decision_tree_test -r 1
```
