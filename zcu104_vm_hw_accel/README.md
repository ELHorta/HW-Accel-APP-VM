# **ZCU104 Hardware Acceleration Inside a Virtual Machine** 


Now that we have the hardware kernel that implements the vector addition, it is possible to run the host application inside the AWS Firecracker, with the options of executing the function in the ARM, or accelerate it in the FPGA fabric.

The first step is to libify the accelerated function, generating two shared libraries, one that executes the function in the processor, and another one that executes it in the FPGA. This is done following the LAB 3 instructions. 

After that, using the directions fom LAB 4, it is possible to run the host application inside the VM, poiting to the shared library containing the vector addition function. There are two shared libraries with the same vector addition function, one that runs in software, and other that runs in hardware.




### Vector  Addition Shared Library (no hardware acceleration) 

Copy the folders:



```
cd ~/vaccel-tutorial-code/

sudo wget  "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1oaF7wjBa8O2ktWXzNPz1n5NvyxUrNcyV" -O app_zcu104.tar

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
patch -R vadd_zcu104/vector_add/vector_add.cpp vector_vadd.patch
```

Create the shared library with hardware acceleration and copy it to the /tmp folder:

```
cd vadd_zcu104/
cd build
cmake ../
make
cp vector_add/libvector_add.so /tmp/
```


Run the application:
```
cd ../../
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

The following steps are similar to the LAB 4 (Targeting ARM Processors) found [here](https://github.com/ELHorta/HW-Accel-APP-VM/tree/main/vaccel_install#lab-4-targeting-arm-processors).

If the network interface "tapTestFc" is not present, then follow these steps:

```
sudo ip tuntap add dev tapTestFc mode tap 
sudo ip addr add dev tapTestFc 172.42.0.1/24 
sudo ip link set dev tapTestFc up
```
 
Now it is possible to run the VM:
```
cd ~/frcrk_5.0/
sudo rm fc.sock 
sudo VACCEL_DEBUG_LEVEL=4 VACCEL_BACKENDS=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/plugins/exec/libvaccel-exec.so LD_LIBRARY_PATH=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/src/ ./bin/firecracker --api-sock fc.sock --config-file ./share/config_vsock.json --seccomp-level 0
```

When prompted for a user/passwod to login, just se root, without a password. 

The next step is to copy the executables and binaries to a new folder on the VIrtual Machine:
```
mkdir vm_zcu104
cd vm_zcu104
scp ubuntu@172.42.0.1:~/vaccel-tutorial-code/app_zcu104/libwrapper-args.so .
scp ubuntu@172.42.0.1:~/vaccel-tutorial-code/app_zcu104/wrapper-args-vaccel .
scp ubuntu@172.42.0.1:~/vaccel-tutorial-code/app_zcu104/xrt.ini .
cp ../libvaccel.so .
```

If the systems asks "Are you sure you want to continue connecting (yes/no/[fingerprint])?", answer "yes" and continue.



Before running the applications on the VM, it s necessary to run the vaccelrt-agent in a **second terminal**. More details can be fond [here](https://docs.vaccel.org/vm-example/#running-the-vaccelrt-agent).

The XCLBIN file containing the hardware accelerated function must also be copied to the same folder as the Vaccel agent:
```
cd ~/vaccel_agent
cp ../vaccel-tutorial-code/app_zcu104/krnl_vadd.xclbin .
cp ../vaccel-tutorial-code/app_zcu104/xrt.ini .
sudo rm /tmp/vaccel.sock*
export VACCEL_BACKENDS=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/plugins/exec/libvaccel-exec.so
export LD_LIBRARY_PATH=/home/ubuntu/frcrk_5.0/lib:$LD_LIBRARY_PATH
export VACCEL_AGENT_ENDPOINT=unix:///tmp/vaccel.sock_2048
./vaccelrt-agent -a $VACCEL_AGENT_ENDPOINT
```

Now it is possible to run the application inside Firecracker, on the first terminal:
```
LD_LIBRARY_PATH=. VACCEL_DEBUG_LEVEL=0 VACCEL_BACKENDS=/opt/vaccel/lib/libvaccel-vsock.so ./wrapper-args-vaccel
```

These result of the vector addition can be seen on this terminal:
```
Initialized session with id: 1
Operation successful!
A:  0  1  2  3  4  5  6  7  8  9 
+
B:  0  1  2  0  1  2  0  1  2  0 
=
C:  0  2  4  3  5  7  6  8 10  9
```

On the terminal that is running the Vaccel agent, it is possible to see the XCLBIN file being loaded into the FPGA, before the function is called by the application:
```
vaccel ttRPC server started. address: unix:///tmp/vaccel.sock_2048
Server is running, press Ctrl + C to exit
Created session 1
Genop session VaccelId { inner: Some(1) }
INFO: Reading krnl_vadd.xclbin
Loading: 'krnl_vadd.xclbin'
Trying to program device[0]: edge
Device[0]: program successful!
Result (HW): 
0 2 4 3 5 7 6 8 10 9 
Destroying session VaccelId { inner: Some(1) }
Destroyed session 1
```







