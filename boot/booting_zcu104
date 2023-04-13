The image used to boot Ubuntu 22.04 on the ZCU 104 board is available here:

[ZCU104 + Ubuntu 22.04](https://drive.google.com/file/d/1g7Jg-5Ato3a2RrceMUtkCQGb31-4OeyH/view?usp=share_link)

After downloading the image, use a tool such as Balena Etcher, Win32 Disk Imagert, etc., to write the image to the SD Card.

Then, prepare the ZCU104 board for the first boot:
- connect the power adapter
- connect the USB UART cable
- insert the SD Card
- connect the ethernet cable to a network with internet access


Power on the ZCU104 board and wait for Ubuntu to boot.

The first login credentials are:

Username = ubuntu ; password = ubuntu

You will be asked to change the password after the first login.

The next step is to install the required libraries and applications:

```
sudo apt-get update
sudo apt install qemu-system-arm
sudo apt install cpu-checker
sudo apt install cmake
sudo apt install tree
```

The last step is to verify if Virtualization Technology is enabled in order to run Firecracker:
```
sudo kvm-ok
```

You must receive the following:

``$INFO: /dev/kvm exists``\
``$KVM acceleration can be used``



You can now proceed to the [vAccel Installation](https://github.com/ELHorta/VM-Migration-With-Hardware-Acceleration/wiki/vAccel-Installation-(No-Hardware-Acceleration))
