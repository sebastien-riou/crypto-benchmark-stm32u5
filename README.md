# crypto-benchmark-stm32u5
Integration of crypto-benchmark on NUCLEO-STM32U5 board

## Dependencies

### Cortex-M33 Toolchain
This projected as been tested with https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/tag/v14.2.1-1.1 on Ubuntu 24.04.

### Other repositories
Install and build them using the initial setup script:
````
./initial-setup
````

----
**NOTE**

The script build libraries also fr risc-v, so it requires a `riscv-none-elf-gcc` in the path. if you do not want that, comment out riscv builds in the build-all-target scripts. 

----

## How to build and run using CLI
Build benchmark lib, for example:
````
cd ../crypto-benchmark
python3 link_ext.py --goal=small
./buildit on/cortex-m33 mldsa 44
cd ../crypto-benchmark-stm32u5 
````

Build the firmware using make:
````
make clean all
````

Load using:

````
./flash
````

To launch the benchmark and get results, go to `../crypto-benchmark` and run `./get-results`. 
Be patient, the benchmark takes about one minute to execute.

## How to import in STM32CubeIDE
Import the top folder as "General -> Existing Projects into Workspace".
If you want to step into crypto-benchmark functions, you need to build it with debug:

````
cd ../crypto-benchmark
python3 link_ext.py --preset=debug
./buildit on/cortex-m33 mldsa 44 OPEN_SOURCE debug
cd ../crypto-benchmark-stm32u5 
make clean all
````

You can replace OPEN_SOURCE by other supported options, see https://github.com/sebastien-riou/crypto-benchmark.