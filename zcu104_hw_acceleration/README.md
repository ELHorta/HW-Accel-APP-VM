# **ZCU104 Hardware Acceleration** 


The files are modifications of the Xilinx tutoirial

Before running the application using hardware acceleration, The configuration file containing the hardware kernel and the drivers to communicate with the FPGA must be generated using the Vitis tools. The files needed are the description of the hardware kernel in a high level language, such as C, and the the FPGA infrastructure definition.


### HW Kernel (Vitis) 
The hardware kernel must be generated in a machine where the Vitis tools are installed.

The first step is create a folder and copy the required files to this folder:

```
mkdir xclbin_vadd
cd xclbin_vadd 
sudo wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1rOQhjtGtkARlSGK3MssCCIHKSXa_Vgqf' -O zcu104_custom_platform_hw.xsa
sudo wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1Xi5XFbTC74Mn_RW8HoCRiHgtQRxto111' -O krnl_vadd.cpp

```

teste
```
source /tools/Xilinx/Vitis/2022.1/settings64.sh

v++ -t hw --platform zcu104_custom_platform_hw.xsa -c -k krnl_vadd -o krnl_vadd.xo 'krnl_vadd.cpp'

v++ -t hw --platform zcu104_custom_platform_hw.xsa -l -o krnl_vadd.xclbin krnl_vadd.xo
```

The XCLBIN file generation takes approximately 30 minutes.
Here you can find an XCLBIN file ready to be used, containing a hardware kernel of the vector addition function:

```
sudo wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1rOQhjtGtkARlSGK3MssCCIHKSXa_Vgqf' -O zcu104_custom_platform_hw.xsa
```



### Host Application (ZCU104)  

The host application will call the OpenCL APIs that are responsible for the FPGA reconfiguration with the XCLBIN file generated in the previous step.
Here you can find an XCLBIN file ready to be used, containing a hardware kernel of the vector addition function:

```
sudo wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1Hz2ivkogkafMEf8sHQ4V34VFcA7upe0l' -O krnl_vadd.xclbin
```

The 

```
mkdir vadd_krnl_host_app
cd vadd_krnl_host_app
wget sources
g++ -O0 -Wall -g  -I ./src/ src/vadd_host* -lOpenCL -lpthread -lm -o host_vadd
```


If everythiong is correct, 

ubuntu@zynqmp:~/vadd_debug$ ./host_vadd 
INFO: Reading krnl_vadd.xclbin
Loading: 'krnl_vadd.xclbin'
Trying to program device[0]: edge
Device[0]: program successful!
TEST PASSED

Now it is possible to libify the hardware kernel.











