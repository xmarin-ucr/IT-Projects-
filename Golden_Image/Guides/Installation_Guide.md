## Installing VirtualBox 
1. Go to [VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)

2. Download the version appropriate for your host system (e.g., Windows)

3. Follow the installation steps provided by VirtualBox

## Installing Rocky Linux 9

1. Visit the [Rocky Linux Downloads](https://rockylinux.org/pt-PT/download)

2. Download version 9.6 labeled Minimal ISO

## Creating the Virtual Machine (VM)

1. Open VirtualBox

2. Click on New

3. Name the virtual machine (e.g., Golden Image)

4. Under ISO Image, select the Rocky Linux ISO you downloaded

5. In Set up unattended guest OS installation, fill in the required fields. Use a hostname without spaces (e.g., GoldenImage)

6. Under Specify virtual hardware, you can select 2048 MB RAM and 2 CPUs. In this project, 4096 MB RAM and 4 CPUs were used

7. Under Specify virtual disk, select 20 GB (or more, such as 30 GB, depending on your host system’s resources)

## VM Storage Configuration

Right-click the VM name and select Settings

Go to the Storage tab

Ensure the configuration does not show "unattend" it should look like the reference image:[Initial Configuration](/Golden_Image/img/unattend.png)

## First Boot
1. Once the VM is created, click Start
2. A menu will appear.
3. Select your language and region. 

### Custom Partitioning
[Main menu Rocky Linux](/Golden_Image/img/InstalacionRockyMenu.png)

1. In the Rocky Linux main menu, select Installation Destination.
2. Under Storage Configuration, choose Custom
3. Click Done
4. A new screen called Manual Partitioning will appear.
5. For mount points, you can choose Standard Partition or LVM. LVM was selected in this case for its scalability
6. Click Done

Partition layout used:
 
| Partition      | Size       | File System  | LVM
| ----------  | :-------------: | ----: | ----:
| /home   | 1 GiB   | ext4 | Sí
| /var/log   | 2 GiB   | ext4 | Sí
| /var/log/audit  | 2 GiB   | ext4 | Sí
| /var/tmp |  1,41 GiB | ext4 | Sí
| / |  7 GiB | ext4 | Sí
| /tmp |  1 GiB | ext4 | Sí
| /var |  2 GiB | ext4 | Sí
| /boot |  1 GiB | ext4 | No
| /boot/efi |  600 MiB | EFI | No
| swap |  7 GiB | SWAP | No

## Setting the root Password
1. In the Rocky Linux main menu, select Root Password

2. Enter your desired password

3. Note: After entering and confirming the password, it’s normal for no checkboxes to be selected

4. Click Done

## Creating a user
1. Go to the User Creation section in the main menu

2. Fill in the required fields (Full Name, Username, etc.)

3. Click Advanced

4. In Advanced User Configuration, check both boxes

5. The default user ID and group ID will be set to 1000

6. Click Save Changes

7. Click Done

![Advanced User Configuration](/Golden_Image/img/Creating_User.png)

## Final Steps

* Click Begin Installation

* Wait approximately 10–15 minutes for the packages to install

* Once installation is complete, click Reboot System