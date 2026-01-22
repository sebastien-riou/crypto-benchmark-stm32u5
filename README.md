# crypto-benchmark-stm32u5
Integration of crypto-benchmark on NUCLEO-STM32U5 board

## Dependencies

### Cortex-M33 Toolchain
This projected as been tested with https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/tag/v14.2.1-1.1

### crypto-bemchmark
A clone of https://github.com/sebastien-riou/crypto-benchmark repository is expected at the same level as the clone of this repository.

````
cd ..
git clone https://github.com/sebastien-riou/crypto-benchmark
cd crypto-benchmark
````


## How to build and run using CLI
Build using make:

````
cd ../crypto-benchmark
./buildit on/cortex-m33 mldsa 44
cd ../crypto-benchmark-stm32u5 
make clean all
````

Load using:

````
./flash
````

Expected result:

- The Green LED should shine when benchmark starts.
- All LED should shine when benchmark has completed.

## How to import in STM32CubeIDE
Import the top folder as "General -> Existing Projects into Workspace".
If you want to step into crypto-benchmark functions, you need to build it with debug:

````
cd ../crypto-benchmark
./buildit on/cortex-m33 mldsa 44 OPEN_SOURCE debug
cd ../crypto-benchmark-stm32u5 
make clean all
````

You can replace OPEN_SOURCE by other supported options, see https://github.com/sebastien-riou/crypto-benchmark.