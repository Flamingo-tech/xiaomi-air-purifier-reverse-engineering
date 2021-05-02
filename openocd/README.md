# OpenOCD communication via Raspberry Pi 4

Following info is valid for Xiaomi Air Purifier 3H PCB `AC-M6-B01 REV 3-XR`

MCU: `STM32F412RE`

## PCB Pinout

J9

| Pin | Description |
|-----|-------------|
|   1 |         3v3 |
|   2 |         GND |
|   3 |       SWDIO |
|   4 |         CLK |
|   5 |         SWO |
|   6 |          NC |

Next to J9, on the backside of the PCB, there is an unlabeled TestPoint.
This is nRST, Pin7 on the STM32F412RE.
For convenience, this pad can be bridged to the non-connected pin 6 on the pinheader.

## Connections from Raspberry Pi to PCB

See (pinout.xyz) for RasPi pinheader pinout

| PCB            | RasPi   |
| -------------- | ------- |
| J9-1 (3v3)     | 3v3     |
| J9-2 (GND)     | GND     |
| J9-3 (SWDIO)   | GPIO 3  |
| J9-4 (CLK)     | GPIO 2  |
| J9-6 / nRST TP | GPIO 18 |

## OpenOCD usage

Use the provided config file

NOTE: Modify parameters `bcm2835gpio_peripheral_base` and `bcm2835gpio_speed_coeffs` depending on the model.

```sh
$ openocd -f <raspiX-swd.cfg -f /usr/local/share/openocd/scripts/target/stm32f4x.cfg
Open On-Chip Debugger 0.11.0+dev-00135-g87c90393f-dirty (2021-05-02-11:44)
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
adapter srst pulse_width: 100

Info : Listening on port 6666 for tcl connections
Info : Listening on port 1337 for telnet connections
Info : BCM2835 GPIO JTAG/SWD bitbang driver
Info : clock speed 2001 kHz
Info : SWD DPIDR 0x2ba01477
Info : stm32f4x.cpu: hardware has 6 breakpoints, 4 watchpoints
Info : starting gdb server for stm32f4x.cpu on 3333
Info : Listening on port 3333 for gdb connections
```

Connect via telnet to dump flash

```sh
$ telnet localhost 1337
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Open On-Chip Debugger
> flash banks
#0 : stm32f4x.flash (stm32f2x) at 0x00000000, size 0x00000000, buswidth 0, chipwidth 0
#1 : stm32f4x.otp (stm32f2x) at 0x1fff7800, size 0x00000000, buswidth 0, chipwidth 0

> flash read_bank 0 bank0.bin
device id = 0x30006441
flash size = 512 kbytes
wrote 524288 bytes to file bank0.bin from flash bank 0 at offset 0x00000000 in 1.733110s (295.423 KiB/s)

> flash read_bank 1 bank1.bin
flash size = 512 bytes
wrote 512 bytes to file bank1.bin from flash bank 1 at offset 0x00000000 in 0.007722s (64.750 KiB/s)

> exit
```
