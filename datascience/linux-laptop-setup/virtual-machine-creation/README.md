# Making an Ubuntu Server Virtual Machine for Hack Oregon with VirtualBox and Vagrant

## Creating a virtual machine
1. You will need to be connected to the internet. Install VirtualBox 5.0 or later if you haven't already.
2. Download the Ubuntu 16.04.1 LTS 64-bit server ISO file from http://releases.ubuntu.com/16.04.1/ubuntu-16.04.1-server-amd64.iso
3. Open VirtualBox. Create a virtual machine.
    * Call it "Hack Oregon Base v2".
    * Set the operating system to Ubuntu 64.
    * Set the memory to 1024 MiB.
    * Create a 32 GB dynamically allocated VDI disk.
4. Open the 'Settings' wizard.
    * Under 'System', change the pointing device to 'PS/2 mouse'.
    * Under 'Audio', uncheck 'Enable Audio'.
    * Under 'USB', uncheck 'Enable USB Controller'.
5. Go to the 'Storage' settings.
    * Click on the empty CD drive and point it to the Ubuntu server ISO file you downloaded. "Live CD" should be cleared.
    * Click on the SATA controller. Make sure "Use Host I/O Cache" is ***cleared***. I had mysterious installation failures with this option set!
6. Close the Settings wizard.

## Installing Ubuntu Server
1. Start the virtual machine. When it comes up you'll be in a text-based installer. In most cases, you'll be able to accept the defaults.
8. Accept all the defaults till you get to the host name screen.
    * Call the machine 'hackoregon-base-v2'.
    * Set the user full name to 'Hack Oregon'.
    * Set the username to 'vagrant'.
    * Set the password to 'ORturkeyeggs'.
    * Do not encrypt your home directory.
    * Set the time zone.
9. When you come to the 'Partition disks' step, select 'Guided - use entire disk'.
    * There's only one disk to select, so select it.
    * When it asks to write the changes to disk, say 'Yes'.
10. The installer will start again. Accept the defaults until you come to 'Software selection'. Select ***both*** 'standard system utilities' and 'OpenSSH server'.
11. The installer will start again. Accept the defaults until the install is complete. Then select 'Continue' to reboot the virtual machine.
12. When the virtual machine comes up, log in on the console as 'vagrant'.
14. Type `sudo shutdown -h now` to power off the virtual machine. The virtual machine will shut down.
15. You now have a Ubuntu 16.04.1 LTS server virtual machine!

## Installing the Hack Oregon services
1. Start the virtual machine.
2. When the virtual machine console shows a login prompt, log in as 'vagrant'.

2. Type `git clone https://github.com/hackoregon/getting-started`.
3. Type `cd getting-started/datascience/linux-laptop-setup`.
4. Type `git checkout <working branch>`.
6. Type `./0update-upgrade`. This will upgrade all packages to the latest versions and will take some time.
5. Reboot the virtual machine with `sudo reboot`.
5. When the virtual machine is back up, log back in again with `ssh`.
3. Type `cd getting-started/datascience/linux-laptop-setup`.
5. Type `./1core`. This will install the core packages for all users.
6. Install the services. Note that these two scripts must be run in the order shown below.
    * Type `./database-gis-services`. This installs PostgreSQL and PostGIS for all users on the virtual machine. It will add 'vagrant' as a database super-user.
    * Type `./data-science-services`. This will install Miniconda and the data science environment for the 'vagrant' user.

## Installing the VirtualBox Guest Additions and 'vagrantizing' the virtual machine
1. Type `./install-guest-additions`. Follow the on-screen instructions. You do not need to reboot.
2. Type `./vagrantize`. Follow the on-screen instructions. This will prepare the virtual machine for use as a Vagrant box.
9. Shut down the virtual machine with `sudo shutdown -h now`.

## Compacting the virtual machine
This procedure is optional but highly recommended.

1. Open the VirtualBox GUI, go into the "Storage" settings and connect the ISO file for an Ubuntu 16.04.1 LTS desktop. I use Linux Mint 18, but any desktop will work as long as it's based on Ubuntu 16.04.1 LTS.
2. Start the virtual machine. It will come up in the desktop, not in the server you just installed.
3. Open a terminal. Type `sudo apt install zerofree`.
4. Type `sudo zerofree -v /dev/sda1`. This will fill all the unused blocks in the virtual disk with zeroes, resulting in a smaller image when compacted. It will take some time.
5. Shut the virtual machine down with `sudo shutdown -h now` and remove the virtual CD from the drive in the 'Storage' settings.
6. In the host command line, type `VBoxManage list hdds`. You will see a hard disk with a random-looking "UUID" string. Copy this string into the clipboard.
7. Type `VBoxManage modifyhd --compact <paste the UUID here>`. This will also take some time.

## Packaging the virtual machine as a Vagrant box
Reference: <https://www.vagrantup.com/docs/virtualbox/boxes.html>

1. Open a host terminal and type `vagrant package --base "Hack Oregon Base v2" --output hackoregon-base-v2.box`. This will take some time.
2. Copy the `hackoregon-base-v2.box` file to an empty directory. 
3. Go to the directory and type `vagrant box add --name hackoregon-base-v2 hackoregon-base-v2.box`.
4. Type `vagrant init hackoregon-base-v2`. This will create a Vagrantfile.
5. Edit the Vagrantfile and add the port forwarding: guest port 8888 to host localhost port 7777.
6. Type `vagrant up --provision`.

## Exporting the virtual machine
The final step is to export the virtual machine as an Open Virtualization Format (OVF) archive so you can share it with other people.

1. Go into the VirtualBox 'File' menu and select 'Export Appliance'. You will see your Hack Oregon Base virtual machine listed. Select it; if you have any others make sure they are ***not*** selected. Then press 'Next'.
2. Make sure the format is OVF 1.0 and the file extension is '.ova'. Make a note of where VirtualBox is going to store the file! Then press 'Next'.
3. Keep the defaults for the 'Appliance settings' screen and press the 'Export' button. The export will start. You'll get a progress window. It can take quite a while to complete the export!
