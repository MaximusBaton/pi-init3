
# Raspberry Pi Pre Initialization for Debian Stretch

## Purpose

This program lets you set your Raspberry Pi  up solely by writing to the /boot partition (i.e. the one you can write from most computers!).

It allows you to distribute a small .zip file to set up a Raspberry Pi to do anything. You tell the user to unzip it over the top of the Pi's boot partition - the system can set itself up perfectly on the first boot.

This package contains `run-once.d` and `on-boot.d` directories having bash-scripts. These scripts are being executed on start up.

## Trying it out

- Download and write a standard [Raspbian Image](https://www.raspberrypi.org/downloads/raspbian/), e.g. the [Raspbian Stretch Lite](https://downloads.raspberrypi.org/raspbian_lite_latest) (tested on `October 2018` version).
- Copy the content of this project's [boot folder](https://github.com/MaximusBaton/pi-init3/tree/master/boot) to the microSD card's `/boot` partition.
- Remove the SD card and put it into your Pi.

The Raspberry Pi should now boot several times. The first boot takes 2-5 minutes depending on your network, and which model of Raspberry Pi you use (I tested with model 3B+ and ZeroW).

Scripts from `run-once.d` folder are being executed once only, and then moved to `run-once.d/completed` folder.

Scripts from `on-boot.d` folder are being executed every time the devices boots up.


# Building pi-init3

You will need `golang` installed (I'm currently using 1.10)
```
root@hostname:~# sudo apt install golang
```
There is a `Makefile` in the root of this project. Calling 
```
root@hostname:~# make
```
will compile the `main.go` (source code) and create `boot/pi-init3`.

Alternatively, you can do the following
```
root@hostname:~# GOOS=linux GOARCH=arm GOARM=5 go build -o boot/pi-init3 .
```
# How it works

This is really cool. The `cmdline.txt` specifies an `init=/pi-init3` kernel argument to use a custom binary in this package in place of the usual systemd init. That binary holds everything  except for the `cmdline.txt` file (that would be a chicken-egg problem) and the `run-once.d`  which you will modify to script your desired setup.

## How/Why you should incorporate this project into your Raspberry Pi project

 If you have a project you expect someone to run on an RPi (especially if it would be the RPi's single purpose) you could provide your own `run-once.d/` scripts that will clone your project, configure, and install it.

# Credits

Credits go to the following projects:

- [gesellix/pi-init2](https://github.com/gesellix/pi-init2): This is the original fork, the fork-chain that led us here started with,
- [RichardBronosky/pi-init2](https://github.com/RichardBronosky/pi-init2): This is a direct fork (^c^) on which this project is based on,

Any contributions appreciated!

# Troubleshooting
1. **Stuck on boot**
Solution: 
The microSD card's `/boot` partition contains `cmdline.txt` file right after burning a fresh image. Find `root=PARTUUID=##PARTUUID##-02` text in it. Copy `##PARTUUID##` and replace it in [cmdline.txt.orig](https://github.com/MaximusBaton/pi-init3/blob/master/boot/cmdline.txt.orig) file **before** copying [boot folder](https://github.com/MaximusBaton/pi-init3/tree/master/boot) to the microSD card's `/boot` partition.

2. **Scripts from `run-once.d` have not been moved to `run-once.d/completed` folder after boot**
Scripts in `run-once.d` and `on-boot.d` folders **must** have a *unix end-line format* (LF) and not *windows-like* (CR LF).

# Enjoy! :)
