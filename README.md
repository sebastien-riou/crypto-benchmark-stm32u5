# crypto-benchmark-stm32u5

> [!WARNING]
> **Deprecated.** This repository is no longer maintained. crypto-benchmark now runs on the NUCLEO-U5A5ZJ-Q board as
> a [lean-bsp](https://github.com/sebastien-riou/lean-bsp) application:
> [crypto-benchmark](https://github.com/sebastien-riou/crypto-benchmark)'s board glue built with
> [lean-bsp-nucleo-u5a5](https://github.com/sebastien-riou/lean-bsp-nucleo-u5a5), a board support package shared by
> every lean-bsp application. Follow crypto-benchmark's README, section
> ["Benchmarking on a board"](https://github.com/sebastien-riou/crypto-benchmark#benchmarking-on-a-board).
>
> Compared with this project, that build runs the same machine code for every benchmarked function, with the same
> outputs, stack, heap and footprint; average cycle counts moved by less than 1 %, from where the linker places the
> code. This repository stays as it was, for reference.

Integration of crypto-benchmark on NUCLEO-STM32U5 board

## Instead: the lean-bsp build

With `lean-bsp`, `lean-bsp-nucleo-u5a5` and [lean-hwbridge](https://github.com/sebastien-riou/lean-hwbridge) set up as
crypto-benchmark's README describes, from the `crypto-benchmark` directory, for example:

````
pipenv run python benchmark.py --hw-platform nucleo-u5a5 --lib pqm4 --algo mlkem --pset 768
````

The results carry the board's name in lean-bsp, `nucleo-u5a5`, where this repository's carried `stm32u5`. Since
lean-benchmark v0.0.12 the caches are cleared before each timed operation, as this repository's `LBMK_clear_caches`
meant to but never did (link-time optimisation dropped it), so cycle counts are a little higher than this
repository's.

The instructions below are this repository's own, kept as they were.

## Dependencies

### Cortex-M33 Toolchain
This projected as been tested with https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/tag/v14.2.1-1.1 on Ubuntu 24.04.

### Other repositories
This repo is set up as an add-on of [crypto-benchmark](https://github.com/sebastien-riou/crypto-benchmark)'s
`initial-setup`, which builds `crypto-benchmark`'s own library for `cortex-m33`
and then this repo's firmware:
````
cd ../crypto-benchmark
./initial-setup --level=custom --addons=crypto-benchmark-stm32u5
````

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