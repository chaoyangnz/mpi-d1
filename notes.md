Normally, we mention platforms, SOCs, and boards.

Platform means a serial of SOCs, 

For example, 

sun20iw1p1 is the platform for Allwinner D1 and D1s(F133) SOCs.
JH7100 is the platform for StarFive JH7100 SOC.

A SOC may include multiple cores (same or different arch) or DSPs.

For example,
- D1 includes a RISC-V core (T-Head Xuantie C906) and a DSP (Tensilica HiFi4)
- JH7100 includes Dual-core RISC-V (SiFive U74), Tensillica VP-6 DSP and NVDLA engine and other AI engines.

SOC is a physical chip.



Boards are building a system typically like a SBC, its center is the SOC, and then add peripherals, like DRAM, NAND, TF Storage, USB, Ethernet, Bluetooth, WIFI, GPIO,.. all kinds of interfaces and pinouts.

Boards based on D1/D1s:
- Allwinner D1 Neza 
- MangoPi MQ / MQ Pro
- Lichee RV

Boards based on JH7100:
- StarFive VisionFive  
- BeagleV Starlight (Cancelled)
