# **Accelerating Applications Inside Virtual Machines Running on Embedded Platforms** 



This tutorial shows how to run an application inside a VM (Firecracker, from AWS) with hardware acceleration provided by FPGA logic resources. 

It employs Xilinx tools to build the hardware infrastructure and compile the host application. On top of it, the [vAccel](https://vaccel.org/) framework is used to map the accelerated functions to different hardware resources.

The [ZCU 104 Board](https://www.xilinx.com/products/boards-and-kits/zcu104.html) is used to host the hardware platform and contains a Zynq UltraScale+ MPSoC device, in addition to peripherals such as DDR memory, USB/JTAG UART, Micro SD card, Ethernet, etc.



The tutorial is divided into five parts:

[Booting Ubuntu 22.04 on ZCU104](/boot)

[vAccel Installation](/vaccel_install)

[ZCU104 Hardware Platform](/zcu104_hw_platform)

[ZCU104 Hardware Platform](/zcu104_hw_platform)

[ZCU104 Hardware Acceleration](/zcu104_hw_acceleration)

[ZCU104 Hardware Acceleration Inside a Virtual Machine](/zcu104_vm_hw_accel)
