# Introduction

The MultiTech bootloader is used to perform application upgrade either from 
file storage or directly by YMODEM transfer if no file storage is available.

## Size

The size of the bootloader for mDot, MTQ and MTQN is 0x10000 (64KB).

The size of the bootloader for xDot is 0x0D000 (52KB).

To include this bootloader with an application built using mbed-os 6, include
`app_offset` and `bootloader_img` settings in the `mbed_app.json` file.

Example:
```
{
    "target_overrides": {
        "MTS_MDOT_F411RE or MTS_DRAGONFLY_F411RE or MTS_DRAGONFLY_L471QG": {
            "target.app_offset": "0x10000",
            "target.bootloader_img": "bootloaders/mdot-boot.bin"
        },
        "XDOT_L151CC": {
            "target.app_offset": "0x0D000",
            "target.bootloader_img": "bootloaders/xdot-boot.bin"
        }
    }
}
```

# Command Line Interface

The bootloader is equipped with a simple command line interface.  It can be accessed
with the following steps:
    * Connect your favorite terminal program to the virtual serial port provided
      by the USB debug connection between the device and your PC (COMXX on Windows,
      /dev/ttyACMXX on Linux)
    * Set your terminal program to run at 115200 baud
    * Reset the device
    * If using the AT terminal, enter 'm' within 250ms then 't' within 500ms and finally 's' 
	  within 500ms
    * If using the debug port, press any key within 250ms
    * You should see a prompt appear that looks like "bootloader :>"
    * Your bootloader is now in CLI mode and ready to execute commands (see below)

MDot and Dragonfly support the following commands:
```
help: display this message
boot: start user application
upgrade <simple|ymodem>: upgrade to new firmware transferred over serial
transfer <simple|ymodem>: transfer new firmware over serial, but don't flash it
recv simple <file name>: read file into the filesystem over serial
recv ymodem [file name]: read file into the filesystem over serial
send <simple|ymodem> <file name>: send file from the filesystem over serial
flash: flash new firmware that has already been transferred
reset: perform a soft reset of the system
list: list all files in the filesystem
delete <file name>: delete the specififed file from the filesystem
erase: erase entire 2MB external flash - BE SURE YOU WANT TO DO THIS!
```

XDot supports a subset of commands and does not have a help feature.  When no
external flash device is available the XDot supports these commands:
```
boot: start user application
upgrade: upgrade to new firmware transferred over serial
reset: perform a soft reset of the system
```

If an external flash device is available the XDot supports these commands:
```
boot: start user application
upgrade: upgrade to new firmware transferred over serial
transfer: transfer new firmware over serial, but don't flash it
send <file name>: send file from the filesystem over serial
reset: perform a soft reset of the system
list: list all files in the filesystem
delete <file name>: delete the specififed file from the filesystem
erase: erase external flash space used by bootloader
```

# Application Firmware Upgrades

On boot if a file named `fw_upgrade.bin` exists in flash storage the upgrade
process will begin.  This file can be loaded using the `transfer` or `recv`
commands.  It can also be created by the application via the FOTA process.

The upgrade binary must only contain the applic