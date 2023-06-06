# **ZCU104 Hardware Acceleration Platform** 



### Platform Creation ###


The ZCU104 hardware design must be modified to host the accelerators created by the Vitis tool.

It is necessary to create a design that implements a partially reconfigurable area inside the FPGA and the required interfaces to communicate with the microprocessor. 

Use this Xilinx Tutorial to generate the implementation files (or go to the next step, if you want to use pre-generated files):

[Xilinx ZCU104 Platform Creation](https://xilinx.github.io/Vitis-Tutorials/2022-1/build/html/docs/Vitis_Platform_Creation/Design_Tutorials/02-Edge-AI-ZCU104/README.html)

After running Steps 1 and 2 of the Xilinx Tutorial, the files needed will be available. 


### Platform Deployment (SNAP) ###
There are six files that must be copied to a specific folder structure. Along with these files, a manifest file must also be created.
More details can be found on [Xilinx Wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/2057043969/Snaps+-+xlnx-config+Snap+for+Certified+Ubuntu+on+Xilinx+Devices).

The folder structure is shown bellow:

```
/boot/firmware/xlnx-config/pac/hwconfig 
                                     |---- cfg1 
                                             |-- manifest.yaml
                                             |-- zcu104 ---- 
                                                    |-- bootgen.bif
                                                    |-- system.dtb
                                                    |-- system.bit
                                                    |-- pmufw.elf
                                                    |-- fsbl.elf
                                                    └-- bl31.elf
```         




You can also follow these steps to download the pre-generated required files:
```     
cd /boot/firmware/
sudo wget  "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=FILEID' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1M9-USXmDBG16XktjwmCiN8r_g0gb9ASu" -O xlnx-conf.tar
sudo tar -xvf xlnx-conf.tar
sudo rm xlnx-conf.tar
```         



sudo wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1M9-USXmDBG16XktjwmCiN8r_g0gb9ASu' -O xlnx-conf.tar



After copying these files, the xlxn-config snap tool must be used to load the hardware acceleration platform into the ZCU104 board.

To install this tool, just run:

```
sudo snap install xlnx-config --classic
```

This command verifies if the hardware acceleration platform is available for deployment:

```
xlnx-config -q
```

If the hardware acceleration platform is available, it will show this message:

```
PAC configurations present in the system:

| PAC Cfg                       |Act| zcu104 Assets Directory
---------------------------------------------------------------------------------------------------------------
| zcu104-platform-cfg1             |   | /boot/firmware/xlnx-config/pac/hwconfig/cfg1/zcu104
---------------------------------------------------------------------------------------------------------------

* No configuration is currently activated *
```

The following command activates the hardware platform:

```
sudo xlnx-config -a  zcu104-platform-cfg1
```

This command issues the following messages:

```
Activating assets for  zcu104-platform-cfg on the zcu104
* Generating boot binary /boot/firmware/boot1040.bin...
* Updating Multi-boot register
* Updating /var/lib/xlnx-config/active_board with zcu104

* IMPORTANT: Please reboot the system for the changes to take effect.
```

After rebooting the system, it is possible to verify if the hardware platform is activated:

```
xlnx-config -q
```

It must exhibit the following messages:
```
PAC configurations present in the system:

| PAC Cfg                       |Act| zcu104 Assets Directory
---------------------------------------------------------------------------------------------------------------
| zcu104-platform-cfg1          | * | /boot/firmware/xlnx-config/pac/hwconfig/cfg1/zcu104
---------------------------------------------------------------------------------------------------------------
```


The board is now ready to run [host applications](https://github.com/ELHorta/HW-Accel-APP-VM/blob/main/zcu104_hw_acceleration) using hardware acceleration.






