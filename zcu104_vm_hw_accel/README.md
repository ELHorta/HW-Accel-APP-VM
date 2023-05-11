# **ZCU104 Hardware Acceleration Inside a Virtual Machine** 


Now that we have the hardware kernel that implments the vector addition, it is possible to run the host inside the AWS Firecracker, with the options of executing the function in the ARM, or accelerate it in the FPGA fabric.

The first step is to libify the accelerated function, generating two shared libraries, one that executes the function in the processor, and another one that executes it in the FPGA. This is done following the LAB 3 instructions. After that, using the diections fom LAB 4, it is possible to run the host application inside the VM, poiting to the shared library containing the vector addition function. There are two shared libraies with the same vecto addition function, one that uns in software, and other that runs in hardware.




### HW Kernel (Vitis) 

LAB 3

Libify 

Generate two shared libraries (with and without HW Accel)

LAB 4

Firecracker with HW kernel







