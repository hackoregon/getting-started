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
14. Enter `sudo shutdown -h now` to power off the virtual machine. The virtual machine will shut down.
15. You now have a Ubuntu 16.04.1 LTS server virtual machine!

## Installing the Hack Oregon services
1. Start the virtual machine.
2. When the virtual machine console shows a login prompt, log in as 'vagrant'.
3. Enter `sudo visudo`. Enter the 'vagrant' password when 'sudo' asks for it. Then add the line

    `vagrant ALL=(ALL:ALL) NOPASSWD: ALL`
    
    to the bottom of the file. Then press `Control-O`, `Enter` and `Control-X` to save the file. 'sudo' will no longer ask you for a password!
2. Enter `git clone https://github.com/hackoregon/getting-started`.
3. Enter `cd getting-started/datascience/linux-laptop-setup`.
4. Enter `git checkout <working branch>`.
6. Enter `./0update-upgrade`. This will upgrade all packages to the latest versions and will take some time.
5. Reboot the virtual machine with `sudo reboot`.
5. When the virtual machine is back up, log back in again.
3. Enter `cd getting-started/datascience/linux-laptop-setup`.
6. Enter `./all-services`. This is a master script that will run
    * `./1core`. This will install the core packages for all users.
    * `./database-gis-services`. This installs PostgreSQL and PostGIS for all users on the virtual machine. It will add 'vagrant' as a database super-user.
    * `./data-science-services`. This will install Miniconda and the data science environment for the 'vagrant' user.
    * `./install-guest-additions`. This will install the VirtualBox guest additions. Follow the on-screen instructions. You do not need to reboot.
    * `./vagrantize`. This will prepare the virtual machine for use as a Vagrant box. Follow the on-screen instructions.
7. Shut the virtual machine down with `sudo shutdown -h now`.

## Compacting the virtual machine
This procedure is optional but highly recommended.

1. Open the VirtualBox GUI, go into the "Storage" settings and connect the ISO file for an Ubuntu 16.04.1 LTS desktop. I use Linux Mint 18, but any desktop will work as long as it's based on Ubuntu 16.04.1 LTS.
2. Start the virtual machine. It will come up in the live system's desktop, not in the server you just installed.
3. Open a terminal. Enter `sudo apt install zerofree`.
4. Enter `sudo zerofree -v /dev/sda1`. This will fill all the unused blocks in the virtual disk with zeroes, resulting in a smaller image when compacted. It will take some time.
5. Shut the virtual machine down with `sudo shutdown -h now` and remove the virtual CD from the drive in the 'Storage' settings.
6. In the host command line, enter `VBoxManage list hdds`. You will see a hard disk with a random-looking "UUID" string. Copy this string into the clipboard.
7. Enter `VBoxManage modifyhd --compact <paste the UUID here>`. This will also take some time.

## Packaging the virtual machine as a Vagrant box
Reference: <https://www.vagrantup.com/docs/virtualbox/boxes.html>

1. Open a host terminal and `cd` to the directory where you want to store the finished Vagrant 'box' file. Enter `vagrant package --base "Hack Oregon Base v2" --output hackoregon-base-v2.box`. This will take some time.
3. Enter `vagrant box add --name hackoregon-base-v2 hackoregon-base-v2.box`.
4. Enter `vagrant init hackoregon-base-v2`. This will create a Vagrantfile.
5. Edit the Vagrantfile and add the port forwarding: guest port 8888 to host localhost port 7777.
6. Enter `vagrant up --provision`.

## Exporting the virtual machine
The final step is to export the virtual machine as an Open Virtualization Format (OVF) archive so you can share it with other people.

1. Set up port forwarding. In the VirtualBox GUI, select the 'Hack Oregon Base v2' virtual machine and start the 'Settings' wizard. Select 'Network' and then 'Advanced'. Press the 'Port Forwarding' button.
    * Add a rule for SSH: 'SSH' in the 'Name' field, '127.0.0.1' in the 'Host IP' field, '2222' in the 'Host Port' field. Leave the 'Guest IP' field empty and put '22' in the 'Guest Port' field. 
    * Add a rule for Jupyter: 'Jupyter' in the 'Name' field, '127.0.0.1' in the 'Host IP' field, '7777' in the 'Host Port' field. Leave the 'Guest IP' field empty and put '8888' in the 'Guest Port' field. 
1. Go into the VirtualBox 'File' menu and select 'Export Appliance'. You will see your Hack Oregon Base virtual machine listed. Select it; if you have any others make sure they are ***not*** selected. Then press 'Next'.
2. Use OVF 2.0 and make sure the exported file extension is '.ova'. Check the 'Write Manifest File' box. Make a note of where VirtualBox is going to store the file! Then press 'Next'.
3. Keep the defaults for the 'Appliance settings' screen and press the 'Export' button. The export will start. You'll get a progress window. It can take quite a while to complete the export!
