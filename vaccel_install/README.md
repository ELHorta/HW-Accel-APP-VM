# **vAccel Installation (No Hardware Acceleration)** 



The instructions contained here are a summary of the original ones, found at the [vAccel Tutorials](https://github.com/nubificus/vaccel-tutorials).  
More details can be found at [vAccel Documentation](https://docs.vaccel.org/).



### LABs 1~3 (Installation and first examples)


The vAccel Framework installation and first examples are available as a set of lab exercises.

For the ZCU104 board, labs one to three must be done in order to install the framework and first examples.


These labs are the same ones available on the vAccel GitHub, and the instructions must be followed without any modifications, observing that:
* LAB 2: You must include these lines at the beginning of the file "vaccel.c":

```#include <error.h>``` 
```#include <ops/vaccel_ops.h>```

* LAB 3-A: You don't need to clone the reference code if you went through LABs 1 and 2.

* LAB 3-B: During the installation of OpenCL, if it asks to restart some services, just click on "OK"

* LAB 3-C: XRT error message. This happens until you install the Xilinx Hardware Platform (see xxxxxxxx)

Here is the GitHub link:

https://github.com/nubificus/vaccel-tutorials  

  
---  
### LAB 4 (Targeting ARM Processors)

Lab 4 on the vAccel website targets an x86 machine. The modified instructions for running the examples on the ZCU104 can be seen in the following paragraphs.

Create a folder to store the Firecracker files, download and uncompress them:  
```
mkdir frcrk_5.0 
cd frcrk_5.0 
wget  "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1hQF_kHSmi7S_mvXjpHJQGQqhVhG7udmp" -O vaccel_aarch64_Release_v0.5.0
tar -xvf vaccel_aarch64_Release_v0.5.0.tar.gz
```


Update the "rootfs.img" file in "~/frcrk_5.0/share":
```
cd ~/frcrk_5.0/share 
rm rootfs.img
wget  "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1RdjhdEm6ORHTZpAt6zKy4NvDMdHdTvNy" -O rootfs.img
```


The first step is to define a network interface that will be used to transfer files between the host and the VM:

```
sudo ip tuntap add dev tapTestFc mode tap 
sudo ip addr add dev tapTestFc 172.42.0.1/24 
sudo ip link set dev tapTestFc up
```



Run Firecracker 

```
cd ~/frcrk_5.0/
sudo rm fc.sock 
sudo VACCEL_DEBUG_LEVEL=4 VACCEL_BACKENDS=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/plugins/exec/libvaccel-exec.so LD_LIBRARY_PATH=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/src/ ./bin/firecracker --api-sock fc.sock --config-file ./share/config_vsock.json --seccomp-level 0
```

Login = root + ENTER



Open a new terminal.

Get the agent binary and make it executable: 
```
cd
mkdir vaccel_agent
cd ~/vaccel_agent
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1CrIYQGg4TBye9ZVt0ZVt8h-IcW4wl1lq' -O vaccelrt-agent
chmod +x vaccelrt-agent
```


Run on this terminal the agent:
```
cd ~/vaccel_agent
sudo rm /tmp/vaccel.sock_2048 
export VACCEL_BACKENDS=/home/ubuntu/vaccel-tutorial-code/vaccelrt/build/plugins/noop/libvaccel-noop.so
export LD_LIBRARY_PATH=/home/ubuntu/frcrk_5.0/lib:$LD_LIBRARY_PATH
export VACCEL_AGENT_ENDPOINT=unix:///tmp/vaccel.sock_2048
./vaccelrt-agent -a $VACCEL_AGENT_ENDPOINT
```


Run the application on Firecracker:
```
LD_LIBRARY_PATH=. VACCEL_DEBUG_LEVEL=4 VACCEL_BACKENDS=/opt/vaccel/lib/libvaccel-vsock.so ./wrapper-args-vaccel
```




