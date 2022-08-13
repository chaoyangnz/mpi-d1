# mpi-d1
baremetal onMangoPi MQ Pro (D1-H)

## Dev environment

Windows + WSL(Ubuntu)

### USB Driver

- Ubuntu
Install libusb
```
sudo apt-get install libusb-1.0-0-dev
```

USB attach from Windows to WSL

https://docs.microsoft.com/en-us/windows/wsl/connect-usb

Typically like
```
$usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
1-2    1462:7d25  USB Input Device                                              Not attached
1-8    1f3a:efe8  USB Device(VID_1f3a_PID_efe8)                                 Attached - Ubuntu  <-----------------------
1-14   8087:0026  Intel(R) Wireless Bluetooth(R)                                Not attached
2-1    0403:6001  USB Serial Converter                                          Not attached
4-2    046d:c534  USB Input Device                                              Not attached
```

- Windows
https://zadig.akeo.ie

### xfel

Build for Unbuntu

https://github.com/xboot/xfel

### Serial Port USB converter driver

Windows:
Download from allwinner

## OpenSBI

The latest OpenSBI (v1.1) already included D1 support.

git clone https://github.com/riscv-software-src/opensbi.git

As I am going to load opensbi directly to memory by xfel, the info passed by BROM is missing:
- hartid via a0 register
- device tree blob address in memory via a1 register. The address must be aligned to 8 bytes.

But OpenSBI can embed the flat device tree in build. 
Download the DTB file from https://github.com/LZU-OSLab/opensbi/releases/tag/v1.0
You can also edit the DTS file and compile to DTB when you need to add more devices.

Then
```
make PLATFORM=generic FW_FDT_PATH=../d1s.dtb CROSS_COMPILE=../riscv64-linux-x86_64-20200528/bin/riscv64-unknown-linux-gnu-
```

Find the firmware in build/platform/generic/firmware/fw_jump.bin

Its FW_TEXT_START is 0x80000000, and jump to 0x80200000, which is defined in https://github.com/riscv-software-src/opensbi/blob/master/platform/generic/objects.mk

Let's run it:
```
sudo xfel ddr d1
sudo xfel write 0x80000000 opensbi/build/platform/generic/firmware/fw_jump.bin
sudo xfel exec 0x80000000
```
