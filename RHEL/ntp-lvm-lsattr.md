# 1. Configuring NTP
There's both ntp and chrony packages that uses ntpd and chronyd daemons which implements the NTP, we'll be using chronyd
- Install chrony
  - `sudo yum install chronyd` 
- Start service
  - `systemctl start chronyd`
- Make sure it's enabled
  - `systemctl enable --now chronyd` + `systemctl status chronyd`

## Server
  Specify a host, subnet, or network from which to allow NTP connections to a machine acting as NTP server.

  - `allow IP-ADDRESS` >> `/etc/chrony.conf`
  - Restart the chrony service to apply changes
    - `sudo systemctl restart chrony`
  - Allow incoming requests from the firewall
    - `firewall-cmd --permanent --add-service=ntp`
    - `firewall-cmd reload`

## Client
  - Add NTP server address
    - `server NTP-SERVER-IP` >> `/etc/chrony.conf`
  - Restart service to apply changes
    - `sudo systemctl restart chrony`

-------------------------------
* Display current time sources the chronyed is accessing from client side*
  - `chroyc sources`
* Display information about NTP clients
  - `chronyc clients`

# 2. LVM
Storage virtualization that combines multiple hard drives,partitions into a single volume group (VG), which then can be subdivided into logical volumes (LV)
![LVM](https://www.redhat.com/sysadmin/sites/default/files/styles/google_discover/public/2020-03/LVM%20Cropped.jpg?itok=ffbAHr_R)
## Creating a logical volume
1. Allocate a hard disk space to machine
2. List available block devices to make sure it's allocated
  -
    ```
      [root@localhost ~]$ lsblk
      NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sr0          11:0    1 1024M  0 rom  
      nvme0n1     259:0    0   20G  0 disk 
      ├─nvme0n1p1 259:1    0  300M  0 part /boot
      ├─nvme0n1p2 259:2    0    2G  0 part [SWAP]
      └─nvme0n1p3 259:3    0 17.7G  0 part /
      nvme0n2     259:4    0    1G  0 disk 
    ```

3. Prepare the physical device by creating a partition
  - `parted -s /dev/nvme0n2 mkpart primary 1 513`
  - Inspect result: 
    ```
      [root@localhost ~]# lsblk
      NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sr0          11:0    1 1024M  0 rom  
      nvme0n1     259:0    0   20G  0 disk 
      ├─nvme0n1p1 259:1    0  300M  0 part /boot
      ├─nvme0n1p2 259:2    0    2G  0 part [SWAP]
      └─nvme0n1p3 259:3    0 17.7G  0 part /
      nvme0n2     259:4    0    1G  0 disk 
      └─nvme0n2p1 259:5    0  488M  0 part 
    ```
4. Create a physical volume
  -
    ```
      [root@localhost ~]# pvcreate /dev/nvme0n2p1 
      Physical volume "/dev/nvme0n2p1" successfully created.
    ```
  - Inspect result:
    ```
      [root@localhost ~]# pvs
      PV             VG  Fmt  Attr PSize   PFree 
      /dev/nvme0n2p1 vg1 lvm2 a--  484.00m 84.00m
    ```
  
5. Create a volume group called vg1
  -
    ```
      [root@localhost ~]# vgcreate vg1 /dev/nvme0n2p1
      Volume group "vg1" successfully created
    ```
  - Inspect result:
    ```
      [root@localhost ~]# vgs
      VG  #PV #LV #SN Attr   VSize   VFree 
      vg1   1   1   0 wz--n- 484.00m 84.00m
    ```

6. Create a logical volume
  - 
    ```
      [root@localhost ~]# lvcreate -n lv1 -L 400M vg1
      Logical volume "lv1" created.
    ```
  - Inspect result:
    ```
      [root@localhost ~]# lvs
      LV   VG  Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
      lv1  vg1 -wi-a----- 400.00m  
    ```

7. Add the file system
  - Create file system for logical volume
    - `mkfs -t xfs /dev/vg01/lv01`
    - Inspect result:
      ```
        [root@localhost ~]# mkfs -t xfs /dev/vg1/lv1
        meta-data=/dev/vg1/lv1           isize=512    agcount=4, agsize=25600 blks
                =                       sectsz=512   attr=2, projid32bit=1
                =                       crc=1        finobt=1, sparse=1, rmapbt=0
                =                       reflink=1
        data     =                       bsize=4096   blocks=102400, imaxpct=25
                =                       sunit=0      swidth=0 blks
        naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
        log      =internal log           bsize=4096   blocks=1368, version=2
                =                       sectsz=512   sunit=0 blks, lazy-count=1
        realtime =none                   extsz=4096   blocks=0, rtextents=0
      ```
8. Mount it to a local directory
    - `mkdir /mnt/data`
    - `mount /dev/vg1/lv1 /mnt/data`
    - Inspect result:
      ```
        [root@localhost ~]# findmnt --types xfs
        TARGET      SOURCE              FSTYPE OPTIONS
        /           /dev/nvme0n1p3      xfs    rw,relatime,seclabel,attr2,inode64,noquota
        ├─/boot     /dev/nvme0n1p1      xfs    rw,relatime,seclabel,attr2,inode64,noquota
        └─/mnt/data /dev/mapper/vg1-lv1 xfs    rw,relatime,seclabel,attr2,inode64,noquota
      ```
9. Mount it persistently across boots
  - Append this line `/dev/vg01/lv01  /mnt/data  xfs  defaults 0 0` to this file `/etc/fstab`
  - `mount /mnt/data`

# 3. Restrict root access to a file/directory
By making the root himself intentionally restrict his access, where he must intentionally re allow his access.
## Using extended attributes
- List file extended attributes
  - `lsattr`
- Change file attributes
  - `chattr +i` uses +/- to add/remove
    - `a` only append, no overwrites
    - `i` immutable