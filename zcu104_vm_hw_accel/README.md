# **ZCU104 Hardware Acceleration Inside a Virtual Machine** 


Now that we have the hardware kernel that implements the vector addition, it is possible to run the host application inside the AWS Firecracker, with the options of executing the function in the ARM, or accelerate it in the FPGA fabric.

The first step is to libify the accelerated function, generating two shared libraries, one that executes the function in the processor, and another one that executes it in the FPGA. This is done following the LAB 3 instructions. 

After that, using the directions fom LAB 4, it is possible to run the host application inside the VM, poiting to the shared library containing the vector addition function. There are two shared libraries with the same vector addition function, one that uns in software, and other that runs in hardware.




### Vector  Addition Shared Library (no hardware acceleration) 

Copy the folders:



```
cd ~/vaccel-tutorial-code/

sudo wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1ORrqUtg9ZYkSJXHApifj7RfHGfyvmlOE' -O app_zcu104.tar

tar -xvf app_zcu104.tar 
```

Create the shared library and copy it to the /tmp folder:

```
cd app_zcu104/vadd_zcu104/
mkdir build 
cd build
cmake ../
make
cp vector_add/libvector_add.so /tmp/
```

Compile the main application:
```
cd ../../
make clean
make wrapper-args-vaccel
```


Run the application:
```
VACCELL_DEBUG_LEVEL=4 LD_LIBRARY_PATH=.:../vaccelrt/build/src/ VACCEL_BACKENDS=../vaccelrt/build/plugins/exec/libvaccel-exec.so ./wrapper-args-vaccel
```

The output should be this one:


```
Initialized session with id: 1
Result (SW): 
0 2 4 3 5 7 6 8 10 9 
Operation successful!
A:  0  1  2  3  4  5  6  7  8  9 
+
B:  0  1  2  0  1  2  0  1  2  0 
=
C:  0  2  4  3  5  7  6  8 10  9
```



### Vector  Addition Shared Library (hardware acceleration) 


Modify the source code:

```
patch -R app_zcu104/vadd_zcu104/vector_add/vector_add.cpp vector_vadd.patch
```

Create the shared library with hardware acceleration and copy it to the /tmp folder:

```
cd vadd_zcu104/
mkdir build 
cd build
cmake ../
make
cp vector_add/libvector_add.so /tmp/
```


Run the application:
```
VACCELL_DEBUG_LEVEL=4 LD_LIBRARY_PATH=.:../vaccelrt/build/src/ VACCEL_BACKENDS=../vaccelrt/build/plugins/exec/libvaccel-exec.so ./wrapper-args-vaccel
```

The output should be this one:


```
Initialized session with id: 1
INFO: Reading krnl_vadd.xclbin
Loading: 'krnl_vadd.xclbin'
Trying to program device[0]: edge
Device[0]: program successful!
Result (HW): 
0 2 4 3 5 7 6 8 10 9 
Operation successful!
A:  0  1  2  3  4  5  6  7  8  9 
+
B:  0  1  2  0  1  2  0  1  2  0 
=
C:  0  2  4  3  5  7  6  8 10  9

```




### Running the Host Application on Firecracker








