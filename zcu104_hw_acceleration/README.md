# **ZCU104 Hardware Acceleration** 


### HW Kernel (Vitis) 

source /tools/Xilinx/Vitis/2022.1/settings64.sh

v++ -t hw --platform zcu104_custom_platform_hw.xsa -c -k krnl_vadd -I'src' -o krnl_vadd.xo 'src/krnl_vadd.cpp'

v++ -t hw --platform zcu104_custom_platform_hw.xsa -l -o krnl_vadd.xclbin krnl_vadd.xo


### Host Application (Vitis)  


g++ -O0 -Wall -g  -I ./src/ src/vadd_host* -lOpenCL -lpthread -lm -o host_vadd







