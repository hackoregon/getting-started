# Making an Ubuntu Server Virtual Machine for Hack Oregon in VirtualBox

## Creating a virtual machine
1. You will need to be connected to the internet. Install VirtualBox 5.0 or later if you haven't already.
2. Download the Ubuntu 16.04.1 LTS 64-bit server ISO file from http://releases.ubuntu.com/16.04.1/ubuntu-16.04.1-server-amd64.iso
3. Open VirtualBox. Create a virtual machine.
    * Call it "Hack Oregon Base".
    * Set the operating system to Ubuntu 64.
    * Set the memory to 1024 MiB.
    * Create a 32 GB dynamically allocated disk.
4. Open the 'Settings' wizard.
    * Under 'System', change the pointing device to 'PS/2 mouse'.
    * Under 'Audio', uncheck 'Enable Audio'.
    * Under 'USB', uncheck 'Enable USB Controller'.
5. Go to the 'Storage' settings.
    * Click on the empty CD drive and point it to the Ubuntu server ISO file you downloaded.
    * Click on the SATA controller. Make sure "Use Host I/O Cache" is ***cleared***. I had mysterious installation failures with this option set!
6. Go to the 'Network' settings.
    * Select 'Advanced'. Then press the 'Port Forwarding' button.
    * Create a rule with 'SSH' as the name, 'TCP' as the protocol, 127.0.0.1 as the Host IP, 2222 as the host port and 22 as the guest port. Leave the guest IP blank. This enables `ssh` into the guest.
    * Create a rule with 'Jupyter' as the name, 'TCP' as the protocol, 127.0.0.1 as the Host IP, 7777 as the host port and 8888 as the guest port. Leave the guest IP blank. This enables browsing to a Jupyter notebook server in the guest.
6. Close the Settings wizard.

## Installing Ubuntu Server
1. Start the virtual machine. When it comes up you'll be in a text-based installer. In most cases, you'll be able to accept the defaults.
8. Accept all the defaults till you get to the host name screen.
    * Call the machine 'hackoregon-base'.
    * Set the user full name to 'Hack Oregon'.
    * Set the username to 'hack'.
    * Set the password to 'ORturkeyeggs'.
    * Do not encrypt your home directory.
    * Accept the default time zone.
9. When you come to the 'Partition disks' step, select 'Guided - use entire disk'.
    * There's only one disk to select, so select it.
    * When it asks to write the changes to disk, say 'Yes'.
10. The installer will start again. Accept the defaults until you come to 'Software selection'. Select ***both*** 'standard system utilities' and 'OpenSSH server'.
11. The installer will start again. Accept the defaults until the install is complete. Then select 'Continue' to reboot the virtual machine.
12. When the virtual machine comes up, log in on the console as 'hack'.
13. Type `ssh-keygen` to generate secure shell keys. Answer all the prompts with `Enter`. Secure shell login is now enabled.
14. Verify that secure shell login works. Open a host terminal and type `ssh hack@localhost -p2222`.

    The first time you do this, you'll see something like this:
    ```
    $ ssh hack@localhost -p2222
    The authenticity of host '[localhost]:2222 ([127.0.0.1]:2222)' can't be established.
    ECDSA key fingerprint is SHA256:DfFzzcB/WnSaFjpM/8r2miQgIRjWoRSlfFYsOE6psFc.
    Are you sure you want to continue connecting (yes/no)?
    ```

    Enter 'yes'. you'll see
    ```
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '[localhost]:2222' (ECDSA) to the list of known hosts.
    ```

    Then you'll get prompted for a password. Enter the 'hack' password and you'll be logged in to the server.
13. Type `sudo apt install -y build-essential dkms` in the secure shell session. You'll be asked for the 'hack' password again.
13. When the install finishes, type `sudo mkdir -p /mnt/cdrom`. This creates a mount point for the guest additions ISO file.
13. Go into the 'Devices' menu above the virtual machine console. Select 'Insert Guest Additions CD'. Then type `mount /dev/sr0 /mnt/cdrom` to mount the ISO. You'll see

    ```
    hack@hackoregon-base:~$ sudo mount /dev/sr0 /mnt/cdrom
    mount: /dev/sr0 is write-protected, mounting read-only
    ```
14. Type `sudo /mnt/cdrom/VBoxLinuxAdditions.run`. The guest additions will be installed.
14. Type `sudo shutdown -h now` to power off the virtual machine. You'll be logged out of the secure shell session and the virtual machine will shut down.
15. You now have a Ubuntu 16.04.1 LTS server virtual machine! Go to the VirtualBox "Snapshots" page and take a snapshot, so you can get back to this state.

## Installing the Hack Oregon services
1. Start the virtual machine.
2. When the virtual machine console shows a login prompt, open a terminal on the host and type `ssh hack@localhost -p2222`.

2. Type `git clone https://github.com/hackoregon/linux-laptop-setup`.
3. Type `cd linux-laptop-setup`.
4. Type `git checkout <working branch>`.
6. Type `./0update-upgrade`. This will upgrade all packages to the latest versions and will take some time.
5. Reboot the virtual machine with `sudo reboot`.
5. When the virtual machine is back up, log back in again with `ssh`.
6. Type `cd linux-laptop-setup`.
5. Type `./1core`. This will install the core packages for all users.
6. Type `./database-gis-services`. This installs PostgreSQL and PostGIS for all users on the virtual machine. It will add 'hack' as a database super-user.
7. Type `./data-science-services`. This will install Miniconda and the data science environment for the 'hack' user.
8. Test the Jupyter notebook server.
    * Type
        ```
        source ~/.bashrc
        activate-data-science
        jupyter notebook --ip 0.0.0.0 --no-browser
        ```

        You will see
        ```
        hack@hackoregon-base:~/linux-laptop-setup$ source ~/.bashrc 
        hack@hackoregon-base:~/linux-laptop-setup$ activate-data-science 
        (data-science) hack@hackoregon-base:~/linux-laptop-setup$ jupyter notebook --ip 0.0.0.0 --no-browser
        [I 14:16:51.469 NotebookApp] [nb_conda_kernels] enabled, 2 kernels found
        [I 14:16:51.576 NotebookApp] [nb_anacondacloud] enabled
        [I 14:16:51.580 NotebookApp] [nb_conda] enabled
        [I 14:16:51.632 NotebookApp] ✓ nbpresent HTML export ENABLED
        [W 14:16:51.633 NotebookApp] ✗ nbpresent PDF export DISABLED: No module named 'nbbrowserpdf'
        [I 14:16:51.638 NotebookApp] Serving notebooks from local directory: /home/hack/linux-laptop-setup
        [I 14:16:51.638 NotebookApp] 0 active kernels 
        [I 14:16:51.639 NotebookApp] The Jupyter Notebook is running at: http://0.0.0.0:8888/
        [I 14:16:51.639 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
        ```
    * On the host, browse to <http://localhost:7777>. You will be connected to the notebook server in the guest.
    * Verify that the notebook server is running.
    * Close the browser window / tab.
    * In the terminal, type `Control-C` twice to shut down the notebook server. Then type `deactivate-data-science` to deactivate the `data-science` environment.
9. Shut down the virtual machine with `sudo shutdown -h now`. Take another snapshot.

## Exporting the virtual machine
The final step is to export the virtual machine as an Open Virtualization Format (OVF) archive so you can share it with other people.

1. Go into the VirtualBox 'File' menu and select 'Export Appliance'. You will see your Hack Oregon Base virtual machine listed. Select it; if you have any others make sure they are ***not*** selected. Then press 'Next'.
2. Make sure the format is OVF 1.0 and the file extension is '.ova'. Make a note of where VirtualBox is going to store the file! Then press 'Next'.
3. Keep the defaults for the 'Appliance settings' screen and press the 'Export' button. The export will start. You'll get a progress window. It can take quite a while to complete the export!
