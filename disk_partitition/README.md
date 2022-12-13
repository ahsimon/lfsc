# Linux Notes

## Filesytem

### Disc Partitioning

Common Disk Types

* SATA (Serial Advanced Technology Attachment)
* SCSI (Small Computer Systems Interface)
* SAS  (Serial Attached SCSI)
* USB  (Universal Serial Bus)
* SDD  (Solid State Drives)
* IDE,EIDE (Integrated Device Electronics /Enhanced IDE)

* **GTP** GIUD Partition Table: 
    * GTP is based on UEFI(Unified Extensible Firmware Interface). It may have up 128 primary partitions. Partitions can be setup 2^33 TB

* **MRB** Master Boot Record:     
    * MRB SCSI Interface. Partitions limit is 2TB. All partitions greater than four are logical partitions 

### Common partitions
|   |   |
|---|---|
| /boot|   | 
| / |used for filesystem   | 
| /home  |   |   
| swap  |extension for physical memory. Recomendation swap space equal to physical memory (somethimes twice)   |



### Naming Disk Devices/Nodes

Device nodes for SCSI and SATA disks follow a simple xxy[z] naming convention, where xx is the device type (usually sd), y is the letter for the drive number (a, b, c, etc.), and z is the partition number

* **/dev**:  Disk devices nodes directory
* **/dev/sda** is the first hard disk
* **/dev/sdb** is the second hard disk
* **/dev/sdb1** is the first partition on the second disk
* **/dev/sdc4** is the fourth partition on the third disk


```sh
# format the partion /dev/sda9
sudo mkfs.ext4 /dev/sda9

# show your current available disk device nodes 
sudo ls -l /dev
```

  
### blkid

* **blkid**:  shows information about partitions
* **slbkl**: shows block device information in tree format

```sh
# show the /dev/sda8 partition info (GPT)
sudo blkid /dev/sda8

# show the /dev/sdb2 partition info (MBR)
sudo blkid /dev/sdb2

# show information label about root
sudo blkid -L root
```

c
# show device information in tree format
slblik
```

### Backing Up and Restoring Partition Tables

* **fdisk**: menu driven partition table editor
* **sfdisk**: partition editor for scripting
* **parted**: partition editor. Can create, remove, resize and move partitions.  
* **gparted**: GUI parted editor
* **gdisk**: user for GTP systems, can also works with MRB systems
* **sgdisk**: scripting gdisk 
* **dd**: used for converting and copying files. Simple type error or misused option could destroy your entire disk  

```sh
# back up the MRB
dd if=/dev/sda of=mrbackup bs=512 count 1

# restore just the primary partition MRB table 
sudo dd if=mrbackup of=/dev/sda bs=512 count=1

#backup using sgdisk
sudo sgdisk -p /dev/sda

#restore using sgdisk
sudo sgdisk -p /dev/sda
```
  
### fdisk

 **fdisk**: can create new partitions either primary or logical within an extended partition. Write the new partition table to disk and then format and mount the new partitions 

```sh
# show the disk geometry
sudo fdisk -l /dev/sdc | grep -i sector
```

```sh
  sudo fdisk /dev/sdb
```
#### Options:

* **m**: Display menu
* **p**: List partition table
* **c**: Create new partition
* **d**: Delete partition
* **t**: Change partition type
* **w**: Write new partition
* **q**: Quit without making changes
 
No actual changes are made until you write the partition table to the disk **w**. If something is wrong, you can jump out safely **q**
The system will use the new partition table until reboot

```sh
  # try and read the the revised partition table
    sudo partprobe -s

  # examine what partitions the operating system is currently aware of
    cat /proc/partitions
```

```sh
  # list primary partition
  fdisk -l /dev/sda 
  
  # format to ext4 /dev/sda3 partition 
  mkfs.ext4 /dev/sda3
```
```sh
  # create a file full of zeros 1 GB in length
  dd if=/dev/zero of=imagefile bs=1M count=1024
  
  # put a filesystem on it 
  mkfs.ext4 imagefile

  # mount it somewhere
  mkdir mntpoint
  sudo mount -o loop imagefile mntpoint

  # unmount it with
  sudo unmount mntpoint 

  # use the loop option to mount
  sudo losetup /dev/loop2 imagefile
  sudo mount /dev/loop2 mntpoint

  #run fdisk on imagefile
  sudo fdisk -C 130 imagefile  
```

#### Options:

* **m** list of commands
* **a** edit bsd disklabel
* **c** toggle the dos compatibility flag
* **d** delete a partition
* **g** create a new empty GPT partition table
* **G** create an IRIX (SGI) partition table
* **l** list known partition types
* **m**   print this menu
* **n**   add a new partition
* **o**   create a new empty DOS partition table
* **p**   print the partition table
* **q**   quit without saving changes
* **s**   create a new empty Sun disklabel
* **t**   change a partition's system id
* **u**   change display/entry units
* **v**   verify the partition table
* **w**   write table to disk and exit
* **x**   extra functionality (experts only)

```sh
  # find the fist free loop device
  sudo losetup -f
  
  # associate the imagefile with the fist free loop device (/dev/loop1)
  sudo losetup /dev/loop1 imagefile

  # create s disk partition label on the loop device (imagefile)
  sudo parted -s /dev/loop1 mklabel msdos

  # create three primary partitons on the loop device
  sudo parted -s /dev/loop1 unit MB mkpart primary ext4 0 256
  sudo parted -s /dev/loop1 unit MB mkpart primary ext4 256 512
  sudo parted -s /dev/loop1 unit MB mkpart primary ext4 512 1024
  
  # check the partition table
  fdisk -l /dev/loop1

  # find device nodes have been created
  ls -l /dev/loop1*

  # put diferent filesystems on the partitions created

  sudo mkfs.ext3 /dev/loop1p1
  sudo mkfs.ext4 /dev/loop1p3
  sudo mkfs.vfat /dev/loop1p3

  # mount all three filesystems 
  mkdir mnt1 mnt2 mnt3
  sudo mount /dev/loop1p1 mnt1
  sudo mount /dev/loop1p2 mnt2
  sudo mount /dev/loop1p3 mnt3

  # show the filesystems are available
  df -Th

  # delete the filesystems
  sudo unmount mnt1 mnt2 mnt3
  rmdir mnt1 mnt2 mnt3
  sudo losetup -d /dev/loop1
```

