# VirtualBox - just VBox things
Some items needed for VirtualBox installations.

Install and create Vbox manually, or use Packer.

## TODO - for packer side
`xset dpms 0 0 0 && xset s noblank && xset s off`

That stopped powering off..

## **VirtualBox addons**
This is needed to get host<->guest copy-pasting to work.
When running the VM, select "Insert Guest Additions.." from menu.
Some times it automounts it nicely into `/media/cdrom0`, and sometimes
it doesn't work. You can try to mount the disk manually:

```
    sudo mkdir /media/vboxcdrom
    sudo mount /dev/cdrom /media/vboxcdrom
    sudo /media/vboxcdrom/VBoxLinuxAdditions.run
```

Add ur user to the group so you won't need *sudo* to access the shared folders:

```
sudo gpasswd -a <username> vboxsf
reboot
# Now you can access /media/sf_.../
```

Find Guest IP:

```
VBoxManage guestproperty get VMNAME "/VirtualBox/GuestInfo/Net/0/V4/IP"
```

But sometimes the virtual CD-drive just doesn't work. Last resort is to
download the ISO (correct version, like `VBoxGuestAdditions_5.0.16.iso`) and make it available in the VM via a shared folder.
That ISO can be mounted `mount -o loop <filename.iso> /mnt/disk` (target path must exist OFC).

## Use host I/O cache with REAL/Metal linux
If you are using a real Linux installation as a VM remember to tick the "Use Host I/O Cache" box in VBox Settings at *Storage / Controller:SATA* for the hard disk.
Without this in my UEFI laptop VBox would fail to write to device every now and then.

## Virtualbox sharedfolders gone missing?
By default shared folder should be at `/media/sf_<name_of_share_you_gave>`.

But sometimes it never appears.
To fix is, this command recompiles the guest additions modules when the kernel changes and you don't have DKMS installed.

    sudo /etc/init.d/vboxadd setup

## Install separate networking on virtualbox VM
Create host-only network: IP: 10.10.4.1, network mask 255.255.255.0, and turn DHCP off. Assign that to the VMs second network.

If u want DHCP on, use values

```
10.10.40.1
255.255.255.0
10.10.40.2
10.10.40.20
```

Then on the VM:

    sudo apt-get install ssh
    sudo nano /etc/network/interfaces

Should be like this:

```
# /etc/network/interfaces on the VBox machine
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
address 10.10.4.15
netmask 255.255.255.0
network 10.10.4.0
broadcast 10.10.4.255
```

The address section can be altered to be whatever number you desire, so long as it is prefixed 10.10.4.x. Restart the bastard and test:

```
sudo systemctl restart networking
sudo apt-get update  # check that NAT connection still works
```



# How to boot from Host (Win10) to Guest real/metal Linux in another partition
Install Linux on a partition (use swap too). Think about backing up Host&Guest.
What we will do is:
  * In Win: Create virtual disk using VBoxManage.exe
  * In Lin: Create GRUB `boot.iso`

### Create virtual disk using VBoxManage.exe
In admin prompt:

```
    # List diskdrive numbers
    wmic diskdrive list brief /format:list
    # Use HD numbers to create VBox disk image
    cd "C:\Program Files\Oracle\VirtualBox"
    VBoxManage internalcommands createrawvmdk -filename "c:\opt\debian_real_hd.vmdk" -rawdisk \\.\PhysicalDrive0
```

Replace the file path with wherever you want to put your VMDK file. Replace the 0 in `\\.\PhysicalDrive0` with your disk number.

### In Lin: Create GRUB
Inside Linux we will duplicate and edit the GRUB configuration and create an ISO file that will be fed to the VirtualBox so that it finds the correct HD and partition to use for the VM.

```
    # Without xorriso grub fails silently with dealing with iso, and mtools creates efi.img
    # This stuff seemed to be bit buggy, and probably has changed already.
    sudo apt-get install xorriso mtools
    mkdir -p /opt/iso/boot/grub
    cp /usr/lib/grub/i386-pc/* /opt/iso/boot/grub
    # Bugginess forced me to deal with efi-stuff at some point on fresh
    # laptop with UEFI running Debian Jessie. But shouldn't be necessary in general(?)
    # cp /usr/lib/grub/x86_64-efi/* /opt/iso/boot/grub

    # Copy an existing grub config
    cp /boot/grub/grub.cfg /opt/iso/boot/grub
    # Edit the cfg file so that it does not specify the HOSTs hard drive!
    # The Host being the one that will run this Linux as a VM in the future.
    # One must never try to boot into the Host via this boot.iso!
    grub-mkrescue -o boot.iso /opt/iso/
```

Then get that `boot.iso` to your Windows host (via cloud, USB, etc.).

### In Win: Create VM using the VMDK and BOOT.ISO
Create new VM and “Use existing virtual hard drive file”; choose the VMDK file.

Note: VirtualBox had to be run as admin, which means the VM files were created under admin
users home dir, which means I have to run VirtualBox as admin every time I want
to boot into this VM (how nice).

Set the `boot.iso` into the virtual cdrom in Settings.
Select your new virtual machine in the sidebar and click the Settings button. Under Storage, select "Controller: IDE" and click the plus sign next to it. Press "Choose Disk" and navigate to the boot.iso file you copied from Linux earlier. Click OK and return to the main VirtualBox screen.

Start VM and GRUB menu should pop up. It will be using the "cfg" file you created earlier in Linux. Log in and install VirtualBox GuestAdditions.

Note: NEVER use the Windows partition from inside the VM. In fact, I recommend removing it from your fstab file so it never pops up in Linux, thus keeping you much safer.


### Some notes on using VBoxManage
To see running virtual machines running and their IPs:

```
   vboxmanage list runningvms
   VBoxManage guestproperty enumerate <vmname>
```
