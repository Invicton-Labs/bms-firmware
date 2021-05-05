# Libre Solar BMS Firmware

![build badge](https://github.com/LibreSolar/bms-firmware/actions/workflows/platformio.yml/badge.svg)

This repository contains the firmware based on [Zephyr RTOS](https://www.zephyrproject.org/) for the different Libre Solar Battery Management Systems.

**Warning:** This firmware is under active development. Even though we try our best not to break any features that worked before, not every commit is fully tested on every board before including it to the main branch. As soon as the status of the firmware is considered stable enough, we will test the core features on all boards and generate a release.

## Supported devices

- Libre Solar 3-5s (12V) BMS with TI bq76920: [BMS-5S50-SC](https://github.com/LibreSolar/bms-5s50-sc)
- Libre Solar 6-15s (24-48V) BMS with TI bq76930/40: [BMS-15S80-SC](https://github.com/LibreSolar/bms-15s80-sc)
- Libre Solar 3-8s (12V/24V) BMS with Intersil ISL94202: [BMS-8S50-IC](https://github.com/LibreSolar/bms-8s50-ic)

## Building and flashing the firmware

This repository contains git submodules, so you need to clone (download) the repository by calling:

```
git clone --recursive https://github.com/LibreSolar/bms-firmware
```

Unfortunately, the green GitHub "Clone or download" button does not include submodules. If you cloned the repository already and want to pull the submodules, run `git submodule update --init --recursive`.

### PlatformIO

It is suggested to use Visual Studio Code and PlatformIO for firmware development, as it simplifies compiling and uploading the code a lot:

1. Install Visual Studio Code and [PlatformIO](https://platformio.org/platformio-ide) to build the firmware.

2. Adjust configuration in `zephyr/prj.conf` if necessary.

3. Select the correct board in `platformio.ini` by removing the comment before the board name under `[platformio]` or create a file `custom.ini` with your personal settings.

4. Connect the board via a programmer. See the Libre Solar website for [further project-agnostic instructions](http://libre.solar/docs/flashing).

5. Press the upload button at the bottom left corner in VS Code.

### Native Zephyr environment

You can also use the Zephyr build system directly for advanced configuration with `menuconfig` or if you need more recently added features.

This guide assumes you have already installed the Zephyr SDK and the west tool according to the [Zephyr documentation](https://docs.zephyrproject.org/latest/getting_started/). Also make sure that `west` is at least at version `0.8.0`.

Now after you cloned the repository (see above), go into the root firmware directory and initialize a west workspace:

        west init -l zephyr

This command will create a `.west/config` file and set up the repository as specified in `zephyr/west.yml` file. Afterwards the following command pulls the Zephyr source and all necessary modules, which might take a while:

        west update

The CMake entry point is in the `zephyr` subfolder, so you need to run `west build` and `west flash` command in that directory.

        cd zephyr

Initial board selection (see `boards subfolder for correct names):

        west build -b <board-name>

Flash with specific debug probe (runner), e.g. J-Link:

        west flash -r jlink

User configuration using menuconfig:

        west build -t menuconfig

Report of used memory (RAM and flash):

        west build -t rom_report
        west build -t ram_report

## API documentation

The documentation auto-generated by Doxygen can be found [here](https://libre.solar/bms-firmware/).

## Unit tests

Writing the tests is still work in progress. New functions should be implemented in test-driven development fashion. Tests for old functions will be added step by step.

Run the tests with the following command:

    platformio test -e unit_test_isl94202 -e unit_test_bq769x0
