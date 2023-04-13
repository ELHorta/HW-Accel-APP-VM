# **ZCU104 Hardware Acceleration Platform** 



### Platform Creation ###


The ZCU104 hardware design must be modified to host the accelerators created by the Vitis tool.

It is necessary to create a design that implements a partially reconfigurable area inside the FPGA and the required interfaces to communicate with the microprocessor. 

Use this Xilinx Tutorial to generate the implementation files:

[Xilinx ZCU104 Platform Creation](https://xilinx.github.io/Vitis-Tutorials/2022-1/build/html/docs/Vitis_Platform_Creation/Design_Tutorials/02-Edge-AI-ZCU104/README.html)

After running Steps 1 and 2 of the Xilinx Tutorial, the files needed will be available. 


### Platform Deployment (SNAP) ###
There are six files that must be copied to a specific folder structure. Along with these files, a manifest file must also be created.

The folder structure is shown bellow:

```
/boot/firmware/xlxn-config/pac/hwconfig 
                                     |---- cfg1 ---- manifest.yaml
                                             |-- zcu104 ---- 
                                                    |-- bootgen.bif
                                                    |-- system.dtb
                                                    |-- system.bit
                                                    |-- pmufw.elf
                                                    |-- fsbl.elf
                                                    └-- bl31.elf
```         

A set o files ready to be used can be found here:

[ZCU104 Platform Files](https://drive.google.com/drive/folders/15MmE8C17n_ZY8ACJh5jjW4K7eWNIAx8c?usp=share_link)


After copying these files, the xlxn-config snap tool is necessary to load the hardware platform created by the Xilinx Tutorial.

To install this tool, just run:

```
sudo snap install xlnx-config --classic
```

This command verifies if the hardware platform is available for deployment:

```
xlnx-config -q
```

If everything is ok, it will show this message:

```
PAC configurations present in the system:

| PAC Cfg                       |Act| zcu104 Assets Directory
---------------------------------------------------------------------------------------------------------------
| zcu104-platform-cfg1             |   | /boot/firmware/xlnx-config/test_pac/hwconfig/cfg1/zcu104
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
| zcu104-platform-cfg1          | * | /boot/firmware/xlnx-config/test_pac/hwconfig/cfg1/zcu104
---------------------------------------------------------------------------------------------------------------
```


The board is now ready to run host applications using hardware acceleration.






