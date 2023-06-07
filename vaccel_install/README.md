# **vAccel Installation (No Hardware Acceleration)** 



The instructions contained here are a summary of the original ones, found at the [vAccel Tutorials](https://github.com/nubificus/vaccel-tutorials).  
More details can be found at [vAccel Documentation](https://docs.vaccel.org/).



### LABs 1~3 (Installation and first examples)


The vAccel Framework installation and first examples are available as a set of lab exercises.

For the ZCU104 board, labs one to three must be done in order to install the framework and first examples.


These labs are the same ones available on the vAccel GitHub, and the instructions must be followed without any modifications, observing that:
* LAB 2: You must include these lines at the beginning of the file "vaccel.c":

```
#include <error.h>
#include <ops/vaccel_ops.h>
```

* LAB 3-A: You don't need to clone the reference code if you went through LABs 1 and 2.

* LAB 3-B: During the installation of OpenCL, if it asks to restart some services, just click on "OK"

* LAB 3-C: XRT error message. This happens when you run the applications. You can ignore this error and continue to the ""libify" section.

Here is the GitHub link:

https://github.com/nubificus/vaccel-tutorials  

  
---  
### LAB 4 (Targeting ARM Processors)

This is a modified version of the Lab 4 available on the vAccel website. This version uses a ZCU104 board to run the applications. 


Te first step is to create a folder to store the Firecracker files, download and uncompress them:  
```
cd
mkdir frcrk_5.0 
cd frcrk_5.0 
wget  "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1hQF_kHSmi7S_mvXjpHJQGQqhVhG7udmp" -O vaccel_aarch64_Release_v0.5.0.tar.gz
tar -xvf vaccel_aarch64_Release_v0.5.0.tar.gz
```
 
 
The original "rootfs.img" file must be updated in "~/frcrk_5.0/share":
```
cd ~/frcrk_5.0/share 
rm rootfs.img
wget  "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1RdjhdEm6ORHTZpAt6zKy4NvDMdHdTvNy" -O rootfs.img
```


A network interface must be defined to transfer files between the host and the VM:
```
sudo ip tuntap add dev tapTestFc mode tap 
sudo ip addr add dev tapTestFc 172.42.0.1/24 
sudo ip link set dev tapTestFc up
```
 
 
Now it is possible to start Firecracker:
```
cd ~/frcrk_5.0/
sudo rm fc.sock 
sudo VACCEL_DEBUG_LEVEL=4 VACCEL_BACKENDS=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/plugins/exec/libvaccel-exec.so LD_LIBRARY_PATH=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/src/ ./bin/firecracker --api-sock fc.sock --config-file ./share/config_vsock.json --seccomp-level 0
```

When prompted for a user/passwod to login, just se root, without a password. 


Before running the applications on the VM, it s necessary to run the vaccelrt-agent in a **second terminal**. 
More details can be fond [here](https://docs.vaccel.org/vm-example/#running-the-vaccelrt-agent).

On the second terminal, get the agent binary and make it executable: 
```
cd
mkdir vaccel_agent
cd ~/vaccel_agent
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1CrIYQGg4TBye9ZVt0ZVt8h-IcW4wl1lq' -O vaccelrt-agent
chmod +x vaccelrt-agent
```


Then, run Vaccel agent:
```
cd ~/vaccel_agent
sudo rm /tmp/vaccel.sock*
export VACCEL_BACKENDS=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/plugins/noop/libvaccel-noop.so
export LD_LIBRARY_PATH=/home/ubuntu/frcrk_5.0/lib:$LD_LIBRARY_PATH
export VACCEL_AGENT_ENDPOINT=unix:///tmp/vaccel.sock_2048
./vaccelrt-agent -a $VACCEL_AGENT_ENDPOINT
```

Now it is possible to run te application inside Firecracker, on the **first terminal**:
```
LD_LIBRARY_PATH=. VACCEL_DEBUG_LEVEL=4 VACCEL_BACKENDS=/opt/vaccel/lib/libvaccel-vsock.so ./wrapper-args-vaccel
```

After that, you can close the virtual machine, on the first terminal:
```
shutdown now
```

And close the vAccel agent on the second terminal, with CTRL+C.


This application can be accelerated using a hardware kernel implemented in the FPGA resources. First, it is necessary to implement a [Hardware Acceleration Platform](https://github.com/ELHorta/HW-Accel-APP-VM/blob/main/zcu104_hw_platform) to host this hardware kernel. 
 

