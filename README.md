# mpi-d1
baremetal on MangoPi MQ Pro (D1-H)

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
$ usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
1-2    1462:7d25  USB Input Device                                              Not attached
1-8    1f3a:efe8  USB Device(VID_1f3a_PID_efe8)                                 Attached - Ubuntu  <-----------------------
1-14   8087:0026  Intel(R) Wireless Bluetooth(R)                                Not attached
2-1    0403:6001  USB Serial Converter                                          Not attached
4-2    046d:c534  USB Input Device                                              Not attached

$ usbipd wsl attach --busid 1-8 -a

```

- Windows
https://zadig.akeo.ie

### xfel

Build for Unbuntu

https://github.com/xboot/xfel

### Serial Port USB converter driver

https://ftdichip.com/drivers/vcp-drivers/

## OpenSBI

The latest OpenSBI (v1.1) already included D1 support.

git clone https://github.com/riscv-software-src/opensbi.git

As I am going to load opensbi directly to memory by xfel, the info passed by BROM is missing:
- hartid via a0 register
- device tree blob address in memory via a1 register. The address must be aligned to 8 bytes.

But OpenSBI can embed the flat device tree in build. 
Download the DTB file from https://github.com/LZU-OSLab/opensbi/releases/tag/v1.0
You can also edit the DTS file and compile to DTB when you need to add more devices.
```
sudo apt-get install device-tree-compiler
dtc -I dts -O dtb -o d1.dtb d1.dts
```
MangoPi Pro DTS: https://github.com/mangopi-sbc/Tina-Linux/blob/main/device/config/chips/f133/configs/pro/linux-5.4/board.dts

Then build
```
make PLATFORM=generic FW_FDT_PATH=../d1s.dtb CROSS_COMPILE=../riscv64-linux-x86_64-20200528/bin/riscv64-unknown-linux-gnu- BUILD_INFO=y
```

Find the firmware in build/platform/generic/firmware/fw_jump.bin

Its FW_TEXT_START is 0x80000000, and jump to 0x80200000, which is defined in https://github.com/riscv-software-src/opensbi/blob/master/platform/generic/objects.mk

Let's run it:
```
sudo xfel ddr d1
sudo xfel write 0x80000000 opensbi/build/platform/generic/firmware/fw_jump.bin
sudo xfel exec 0x80000000
```

References:
- https://bbs.aw-ol.com/topic/1061/%E8%8A%92%E6%9E%9C%E6%B4%BE-mpi-mq1-%E5%85%A8%E5%BF%97-d1s-%E5%BC%80%E5%8F%91%E6%9D%BF%E5%88%9D%E4%BD%93%E9%AA%8C
