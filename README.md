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

Restart the firmware already loaded on the board (reset and run, exits immediately):

````
./run
````

`./run [probe_index] [soft|hard|power]` selects the reset: `soft` (default) is a system
reset requested over SWD, `hard` pulses the NRST pin like the board's RESET button and
recovers a wedged target, `power` power cycles the target through the ST-LINK for a true
power-on reset. See the comments in the script.

Print the UART device to connect to (the ST-LINK virtual COM port):

````
./find-uart
````

With several boards plugged in, pass the ST-LINK serial number as listed by
`STM32_Programmer_CLI -l st-link-only`, for example `./find-uart 002E0034`.

To launch the benchmark and get results, go to `../crypto-benchmark` and run
`./get-results $(cd ../crypto-benchmark-stm32u5 && ./find-uart)`. 
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