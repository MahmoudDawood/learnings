# RHEL Administration ||
## 1. Improving Command-line Productivity

### Creating and Executing Bash Shell Scripts
- `#!/bin/bash` specifies the command interpreter to execute it
- `which commandName` displays command to be executed path name
- `hostname` displays the host name

### Loops
```
for VARIABLE in LIST; do
COMMAND VARIABLE
done
```
- `for i in $(seq start step endIncluded)`

### Exit codes
- 0 >> no error, (1 - 255) >> error
- `echo $?`

* test: checks file types and compare values
```
[user@host ~]$ test 1 -gt 0 ; echo $?
0
``` 
or use [ TEST STATEMENT ]
```
  [ -{eq ne gt ge lt le} ]
```

### If condition
```
if <CONDITION>; then
  <STATEMENT>
    ...
  <STATEMENT>
  elif <CONDITION>; then
    <STATEMENT>
    ...
    <STATEMENT>
  else
    <STATEMENT>
    ...
    <STATEMENT>
  fi
```

### RegEx
*`grep regex location`*

|Option|	Description|
|-------|-------|
|^  | Start of line |
|$  | End of line   |
|.	|The period (.) matches any single character.|
|?	|The preceding item is optional and will be matched at most once.|
|*	|The preceding item will be matched zero or more times.|
|+	|The preceding item will be matched one or more times.|
|{n}	|The preceding item is matched exactly n times.|
|{n,}	|The preceding item is matched n or more times.|
|{,m}	|The preceding item is matched at most m times.|
|{n,m}|	The preceding item is matched at least n times, but not more than m times.|
[:alnum:]|	Alphanumeric characters: '[:alpha:]' and '[:digit:]'; in the 'C' locale and ASCII character encoding, this |is the same as '[0-9A-Za-z]'.|
[:alpha:]|	Alphabetic characters: '[:lower:]' and '[:upper:]'; in the 'C' locale and ASCII character encoding, this is |the same as '[A-Za-z]'.|
|[:blank:]	|Blank characters: space and tab.|
[:cntrl:]|	Control characters. In ASCII, these characters have octal codes 000 through 037, and 177 (DEL). In other |character sets, these are the equivalent characters, if any.|
|[:digit:]	|Digits: 0 1 2 3 4 5 6 7 8 9.|
|[:graph:]	|Graphical characters: '[:alnum:]' and '[:punct:]'.|
[:lower:]|	Lower-case letters; in the 'C' locale and ASCII character encoding, this is a b c d e f g h i j k l m n o p |q r s t u v w x y z.|
|[:print:]|	Printable characters: '[:alnum:]', '[:punct:]', and space.|
[:punct:]|	Punctuation characters; in the 'C' locale and ASCII character encoding, this is! " # $ % & ' ( ) * + , |- . / : ; < = > ? @ [ \ ] ^ _ ' { | } ~. In other character sets, these are the equivalent characters, if any.|
[:space:]	|Space characters: in the 'C' locale, this is tab, newline, vertical tab, form feed,carriage return, and |space.|
[:upper:]|	Upper-case letters: in the 'C' locale and ASCII character encoding, this is A B C D E F G H I J K L M N O P |Q R S T U V W X Y Z.|
|[:xdigit:]|	Hexadecimal digits: 0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f.|
|\b	|Match the empty string at the edge of a word.|
|\B|	Match the empty string provided it is not at the edge of a word.|
|\<	|Match the empty string at the beginning of word.|
|\>	|Match the empty string at the end of word.|
|\w	|Match word constituent. Synonym for '[_[:alnum:]]'.|
|\W	|Match non-word constituent. Synonym for '[^_[:alnum:]]'.|
|\s	|Match white space. Synonym for '[[:space:]]'.|
|\S	|Match non-whitespace. Synonym for '[^[:space:]]'. |

|Option|	Function|
|-------|---------|
|-i |	Use the regular expression provided but do not enforce case sensitivity (run case-insensitive).|
|-v |	Only display lines that do not contain matches to the regular expression.|
|-r |	Apply the search for data matching the regular expression recursively to a group of files or directories.|
|-A |NUMBER 	Display NUMBER of lines after the regular expression match.|
|-B |NUMBER 	Display NUMBER of lines before the regular expression match.|
|-e |	With multiple -e options used, multiple regular expressions can be supplied and will be used with a logical OR.|

## 2. Scheduling Future Tasks 

### Deferred user task
- `at TIMESPEC`  examples:    now +5min,
    teatime tomorrow (teatime is 16:00),
    noon +4 days,
    5pm august 3 2021 
- `atq`    
- `atrm JOBNUMBER`
- `watch atq` watch queue in real time

*options*
- `-c` specify job number
- `-q` specify job queue

### Schedule recurring user jobs
- `Minutes Hours Day-of-month Month Day-of-week Command  ` order
- `crontabl -l` list
- `crontabl -r` remove all
- `crontabl -e` edit
- `*` don't care
- `x-y` range
- `*/x` interval

### Recurring System Jobs
- `/etc/crontab`
- `/etc/corn.d` custom files (Alpha)
- `/etc/cron.hourly/, /etc/cron.daily/, /etc/cron.weekly/, and /etc/cron.monthly/` system executable scripts
-
- `/etc/anacrontab` includes important jobs
- Days, minutes, job id, command (order)

### Systemd timer
- `yum install sysstat` gets triggered every 10mins by default
- `/usr/lib/systemd/system/sysstat-collect.timer.`
- `/etc/systemd/system` Custom timers (Alpha)
- `onCalendar` property
- run `systemctl daemon-reload`

### Managing temporary files
- `systemd-tmpfiles-clean.timer` daemon managing temp files

  **Configuration files can exist in three places:**
    - /etc/tmpfiles.d/*.conf
    - /run/tmpfiles.d/*.conf
    - /usr/lib/tmpfiles.d/*.conf 
    
    - Type, Path, Mode, UID, GID, Age, and Argument
      - `d` use dir
      - `D` create dir or empty it if it exists
    - `systemd-tmpfiles --create /etc/tmpfiles.d/......conf` verify configuration settings

## 3. Tuning system performance
### Tuning profiles

- `yum install tuned` + enable the daemon
- or via web console

- `tuned-adm active`
- `tuned-adm list`
- `tuned-adm profile throughput-performance` switch active profile
- `tuned-adm recommend`
- `tuned-adm off` turn daemon off

### Process priorities
- Nice level: -20(highest) --> 19(lowest), default is 10  

- `ps axo pid,comm,nice --sort=-nice` list process nice levels
- `nice -n 15 sha1sum &` apply a certain nice level
- `renice -n 19 3521` update existing process nice level

## 4. ACL

### ACL Permissions
- `ls -l reports.txt` list a minimal details, + sign refers to extended ACL
- `getfacl reports.txt`
- `setfacl RULE FILE` 
  - `-m` modify 
  - `-x` delete entry
  - `-R` recursive
  - `u:user:perm,g:group:perm`
  - `mask::rw-` masks all maximum permissions except owner user 
  - `--set-file=-` use a file to set acl, - means get it from input

## 5. SELinux Security
- Can be used via web console
- `/etc/selinux/config` defaults
- Enforcing, permissive (warnings), disabled
- `getenforce`
- `setenforce`

- SELinux user, Role, Type, Level, File: order
- `-z` usually displays SELinux contexts

### Changing SELinux context of a file
- `chcon -t httpd_sys_content_t /virtual`
- `restorecon -v /virtual` return to default values
- `semanage fcontext`
  - `-a` add
  - `-d` delete
  - `-l` list
- `semanage-fcontext -a -t httpd_sys_content_t '/dir(/.*)?'` + `restorecon -Rv /dir` to ensure all files have to correct context

### SELinux booleans
- `getsebool -a`
- `setsebool -P role on-off` modifies policy to make modifications persistent
- `semanage boolean -l` reports on whether or not a boolean is persistent`

### Troubleshooting SELinux
- `/var/log/audit/audit.log` has selinux errors
- `sealert -a /var/log/audit/audit.log` produce report of all incidents in the file
- `sealert -l UUID` reports of specific incident
- `ausearch -m AVC -ts recent` seraches /var/log/audit/audit.log
  - `-m` for message type
  - `-ts` time

## 6. Managing basic storage
### Partitioning a disk
**Creating MBR partition**
- `parted /dev/vda` opens interactive shell
  - `print` display partition table
  - `unit` s: sector, B Byte, MiB powers of 2, MB powers of 10
- `parted /dev/vda mklabel` msdos or gpt 
  - `mkpart` primary or extended `help mkpart`
  - `start & end` s, MiB, GiB, TiB, MB, GB, TB, default: MB
  - `quit`

- `udevadm settle` system waits until it detects a new partition

**Creating a GPT partition**
- same as mbr but specifies a partition name
- `parted /dev/vdb mkpart usersdata xfs 2048s 1000MB`

### Deleting a partition
  - `parted /dev/...` + `print` +  `rm n` + `quit`

### Mounting a file system
- `mount /dev/vdb1 /mnt` device + mount point
- `mount` list mounted file systems

### Persistently Mounting File Systems
- `/etc/fstab`
  - Append `UUID=7a20315d-ed8b-4e75-a5b6-24ff9e1f9838   /mountPoint  xfs   defaults   0 0`
  - `systemctl daemon-reload`
  - `mount /mountPoint`
- `lsblk --fs` scan block devices
- `findmnt --verify` verify mounts in /etc/fstab
- `mkfs.xfs /dev/vdb1` format partition with a file system 

### Swap spaces
#### Create a swap space
- Create a partition with a file system type of `linux-swap`.
- Place a swap signature on the device. 
  - `mkswap /dev/vdb2` writes a single block of data at the beginning of the device, leaving the rest of the device unformatted so the kernel can use it for storing memory pages.

- `swapon --show` or `free` list available swap spaces
- `swapon /dev/vdb2` activate swap space

#### Activate swap space persistently
- `UUID=39e2667a-9458-42fe-9665-c5c854605881   swap   swap   defaults   0 0` >> `/etc/fstab`

#### Swap space priority
- `UUID=39e2667a-9458-42fe-9665-c5c854605881   swap   swap   pri=4     0 0` the higher the more priority, default: -2

## 7. Logical volumes
*Physical Device >> Physical Volume >> Volume Group >> Logical Volume*
### Creating a Logical volume
1. Prepare the physical device
  - `parted -s /dev/vdb mkpart primary 770MiB 1026MiB`
  - `parted -s /dev/vdb set 1 lvm on`
2. Create a physical volume
  - `pvcreate /dev/vdb2 /dev/vdb1`
3. Create a volume group
  - `vgcreate vg01 /dev/vdb2 /dev/vdb1`
4. Create a logical volume
  - `lvcreate -n lv01 -L 700M vg01` -l: in extents (4MB), -L: in specified unit
  - Exists in `/dev/vgname/lvname`
5. Add the file system
  - `mkfs -t xfs /dev/vg01/lv01`
  - Mount it persistently
    - `mkdir /mnt/data`
    - `/dev/vg01/lv01  /mnt/data xfs  defaults 1 2` >> `/etc/fstab`
    - `mount /mnt/data`

### Removing a logical volume
  - `umount /mnt/data` + remove it's entry in `/etc/fstab`
  - `lvremove /dev/vg01/lv01`
  - `vgremove vg01`
  - `pvremove /dev/vdb2 /dev/vdb1`

### Reviewing LVM Status Information
- `pvdisplay /dev/..` OR `pvs`
- `vgdisplay vgName` OR `vgs`
- `lvdisplay /dev/..` OR `lvs`

### Extending logical volumes
1. Prepare PD, create PV
  - `parted -s /dev/vdb mkpart primary 1027MiB 1539MiB`
  - `parted -s /dev/vdb set 3 lvm on`
  - `pvcreate /dev/vdb3`
2. Extend VG
  - `vgextend vg01 /dev/vdb3`
  - `vgdisplay vg01`
3. Extend LV
  - `lvextend -L +300M /dev/vg01/lv01`

### Reducing a volume group
**Make a backup**
1. Move the physical extents. 
  - `pvmove /dev/vdb3` relocate any physical extents from the physical volume you want to remove to other physical volumes in the volume group
2. Reduce volume group
  - `vgreduce vg01 /dev/vdb3`

### Extending a Logical Volume and XFS File System
1. Verify that the volume group has space available. 
  - `vgdisplay vg01`
2. Extend the logical volume. 
  - `lvextend -L +300M /dev/vg01/lv01` -l: extents, L: MiBs, + adds, else locates it exactly
3. Extend fs
  - `xfs_growfs /mnt/data`

- `resize2fs /dev/vg01/lv01` expand the file system to occupy the new extended LV

### Extend a logical volume and swap space
1. Verify the volume group has space available. 
2. Deactivate the swap space. 
  - `swapoff -v /dev/vgname/lvname`
3. Extend LV
4.  Format the logical volume as swap space.
  - `mkswap /dev/vgname/lvname`
5. Activate swap space 
  - `swapon -va /dev/vgname/lvname`

## 8. Advanced storage
### Stratis
- Located in `/stratis`
- Uses thin provisioning
**Devices >> Pool >> File Systems**
- `yum install stratis-cli stratisd`
- `systemctl enable --now stratisd`
- `stratis pool create pool1 /dev/vdb` Create pools of one or more block devices
- `stratis pool list`
- `stratis pool add-data pool1 /dev/vdc` add block dev to pool
- `stratis filesystem create pool1 fs1` create a fs of pool
- `stratis filesystem list`
- `stratis filesystem snapshot pool1 fs1 snapshot1` create a snapshot
- `lsblk --output=UUID /stratis/pool1/fs1` get UUID to use in `/etc/fstab`
  - `UUID=31b9363b-add8-4b46-a4bf-c199cd478c55 /dir1 xfs defaults,x-systemd.requires=stratisd.service 0 0`

### VDO
1. Zero Block Elimination
2. Deduplication
3. Compression

- `yum install vdo kmod-kvdo`
- `vdo create --name=vdo1 --device=/dev/vdd --vdoLogicalSize=50G` create a vdo, no size occupies whole physical device.
- `vdo list`
- `vdo status --name=vdo1` 
- `vdostats --human-readable`

## 9. Network-attached storage
### Mounting NFS Shares
1. Identify
  - `mount serverb:/ mountpoint`
2. Mount point
  - `mkdir -p mountpoint`
3. Mount
  - `mount -t nfs -o rw,sync serverb:/share mountpoint` 
- `serverb:/share  /mountpoint  nfs  rw,soft  0 0` >> `/etc/fstab` for persistent
- `unmount mountpoint`

### automounting nfs using automount
1. `yum install autofs`
2. `/shares  /etc/auto....` >> master map `/etc/auto.master.d/.....autofs` base of indirect mounts & mount details
3. `work  -rw,sync  serverb:/shares/work` >> mapping file `/etc/auto....` mount point & option & source location
4. `systemctl enable --now autofs`

### direct maps
 maps an nfs share to an existing absolute path mount point. 
1. `/- /etc/auto....` >> `/etc/auto.master.d/.....autofs` /- is the base directory
2. `work  -rw,sync  serverb:/shares/work` >> `/etc/auto....` mount point & option & source location
 
### indirect wildcard maps
to access any one of those subdirectories using a single mapping entry.
- `*  -rw,sync  serverb:/shares/&`

## 10. controlling the boot process
**Firmware(UEFI/BIOS) >> bootable device(MBR)>> boot loader(grub2) >> kernel**
- `systemctl poweroff`
- `systemctl reboot`

### Systemd Target
|Target |	Purpose|
|--------|-------|
|graphical.target 	|System supports multiple users, graphical- and text-based logins.|
|multi-user.target 	|System supports multiple users, text-based logins only.|
|rescue.target |	sulogin prompt, basic system initialization completed.|
|emergency.target 	|sulogin prompt, initramfs pivot complete, and system root mounted on / read only.|

- `systemctl list-dependencies graphical.target` view dependencies
- `systemctl list-units --type=target --all` list available targets
- `systemctl isolate multi-user.target` switch to different target
  - target must have `AllowIsolate=yes` in it's config, `systemctl cat .....target`
- `systemctl get-default`
- `systemctl set-default .....target`

### Selecting a different target at boot time
1. Boot or reboot the system.
2. Interrupt the boot loader menu countdown by pressing any key (except Enter which would initiate a normal boot).
3. Move the cursor to the kernel entry that you want to start.
4. Press e to edit the current entry.
5. Move the cursor to the line that starts with linux. This is the kernel command line.
6. Append systemd.unit=*target*.target. For example, systemd.unit=emergency.target.
7. Press Ctrl+x to boot with these changes. 

### Resetting the Root Password from the Boot Loader
1. Append rd.break. With that option, the system breaks just before the system hands control from the initramfs to the actual system. 
2. Press Ctrl+x to boot with these changes. 
3. `mount -o remount,rw /sysroot`
4. `chroot /sysroot`
5. `passwd root`
6. `touch /.autorelabel`

### Inspecting logs
View previously failed boots from journalctl
- Logs are kept in `/run/log/journal`
- Store it in `/var/log/jounral`
  - `Storage=persistent` >> `/etc/systemd/journald.conf`
- `journalctl -b -1 -p err` inspect logs of previous boot

### Repairing Systemd Boot Issues
- `systemctl enable debug-shell.service` **Disable it after you're finished**
- `systemd.unit=rescue.target` or `emergence.target` to kernel cli from boot loader
- `nofail` option in an entry in the `/etc/fstab` file permits the system to boot even if the mount of that file system is not successful.

## 11. network security
### configuring the firewall
- /etc/firewalld
- web console
- firewall-cmd

|firewall-cmd commands                               |	explanation|
|----------------------------------------------------|-------------|
| --get-default-zone 	| query the current default zone.|
| --set-default-zone=zone | set the default zone. this changes both the runtime and the permanent configuration.|
| --get-zones 	| list all available zones.|
|--get-active-zones 	|list all zones currently in use (have an interface or source tied to them), along with their interface and source information.
|--add-source=cidr [--zone=zone] 	|route all traffic coming from the ip address or network/netmask to the specified zone. if no --zone= option is provided, the default zone is used.
|--remove-source=cidr [--zone=zone] 	|remove the rule routing all traffic from the zone coming from the ip address or network/netmask network. if no --zone= option is provided, the default zone is used.
|--add-interface=interface [--zone=zone] 	|route all traffic coming from interface to the specified zone. if no --zone= option is provided, the default zone is used.
|--change-interface=interface [--zone=zone] |	associate the interface with zone instead of its current zone. if no --zone= option is provided, the default zone is used.
|--list-all [--zone=zone] 	|list all configured interfaces, sources, services, and ports for zone. if no --zone= option is provided, the default zone is used.
|--list-all-zones 	|retrieve all information for all zones (interfaces, sources, ports, services).
|--add-service=service [--zone=zone] 	|allow traffic to service. if no --zone= option is provided, the default zone is used.
|--add-port=port/protocol [--zone=zone] 	|allow traffic to the port/protocol port(s). if no --zone= option is provided, the default zone is used.
|--remove-service=service [--zone=zone] 	|remove service from the allowed list for the zone. if no --zone= option is provided, the default zone is used.
|--remove-port=port/protocol [--zone=zone] 	|remove the port/protocol port(s) from the allowed list for the zone. if no --zone= option is provided, the default zone is used.|
|--reload 	|drop the runtime configuration and apply the persistent configuration. |

ex
- firewall-cmd --permanent --add-port=82/tcp
  - firewall-cmd --reload (to activate the permanent setting)

### selinux port labeling
- semanage port -l

- to add a port to an existing port label (type)
-  semanage port -a -t port_label -p tcp|udp portnumber
-                -d [delete]
-                -m (modify)

- to view local changes to the default policy
- semanage port -l -c

in selinux >> sealert -a /var/log/audit/audit.log ....

## 12. installing RHEL

### tmux shortcuts 
press and release ctrl+b, and then press the number key of the window you want to access. with tmux, you can also use alt+tab to rotate the current focus between the windows.
|key sequence| 	contents|
|-----------|-------|
|ctrl+alt+f1 |	access the tmux terminal multiplexer.
|ctrl+b 1 	|when in tmux, access the main information page for the installation process.
|ctrl+b 2 	|when in tmux, provide a root shell. anaconda stores the installation log files in the /tmp directory.
|ctrl+b 3 	|when in tmux, display the contents of the /tmp/anaconda.log file.
|ctrl+b 4 	|when in tmux, display the contents of the /tmp/storage.log file.
|ctrl+b 5 	|when in tmux, display the contents of the /tmp/program.log file.
|ctrl+alt+f6 |	access the anaconda graphical interface. 

### automating rhel installation with kickstart
* located at /root/anaconda-ks.cfg or create by kickstart generator website
* ksvalidator package validates kickstart files

- % software
- @ package groups
- @^ environment groups (group or groups)
- %pre before disk partitioning
- %post after installation

#### installation commands
- url --url="..." (url referring to installation media)
- repo --name="..." --baseurl=... (additional package for installation, must be a valid yum repository)
- text: forces a text mode install. 
- vnc: allows the graphical installation to be viewed remotely over vnc. 

#### partitioning commands
- clearpart: removes partitions from the system prior to creation of new partitions. by default, no partitions are removed.
  - clearpart --all --drives=sda,sdb --initlabel

- part: specifies the size, format, and name of a partition.
  - part /home --fstype=ext4 --label=homes --size=4096 --maxsize=8192 --grow

- autopart: automatically creates a root partition, a swap partition, and an appropriate boot partition for the architecture. on large enough drives, this also creates a /home partition.

- ignoredisk: controls anaconda's access to disks attached to the system.
  - ignoredisk --drives=sdc

- bootloader: defines where to install the bootloader.
  - bootloader --location=mbr --boot-drive=sda

- volgroup, logvol: creates lVM volume groups and logical volumes.
  - part pv.01 --size=8192
  - volgroup myvg pv.01
  - logvol / --vgname=myvg --fstype=xfs --size=2048 --name=rootvol --grow
  - logvol /var --vgname=myvg --fstype=xfs --size=4096 --name=varvol

- zerombr: Initialize disks whose formatting is unrecognized. 

#### Network commands
network: Configures network information for target system. Activates network devices in installer environment.

network --device=eth0 --bootproto=dhcp

firewall: Defines the firewall configuration for the target system.

firewall --enabled --service=ssh,http

#### Location and security commands
- lang: Sets the language to use during installation and the default language of the installed system. Required.
  - lang en_US.UTF-8

keyboard: Sets the system keyboard type. Required.
keyboard --vckeymap=us --xlayouts=''

timezone: Defines the timezone, NTP servers, and whether the hardware clock uses UTC.
timezone --utc --ntpservers=time.example.com Europe/Amsterdam

authselect: Sets up authentication options. Options recognized by authselect are valid for this command. See authselect(8).

rootpw: Defines the initial root password.
rootpw --plaintext redhat
or
rootpw --iscrypted $6$KUnFfrTzO8jv.PiH$YlBbOtXBkWzoMuRfb0.SpbQ....XDR1UuchoMG1

selinux: Sets the SELinux mode for the installed system.
selinux --enforcing

services: Modifies the default set of services to run under the default systemd target.
services --disabled=network,iptables,ip6tables --enabled=NetworkManager,firewalld

group, user: Create a local group or user on the system.
group --name=admins --gid=10001
user --name=jdoe --gecos="John Doe" --groups=admins --password=changeme --plaintext

#### Miscellaneous Commands

- logging: This command defines how Anaconda will perform logging during the installation.
  - logging --host=loghost.example.com --level=info

- firstboot: If enabled, the Setup Agent starts the first time the      system boots. The initial-setup package must be installed.
  - firstboot --disabled

- reboot, poweroff, halt: Specify the final action to take when the installation completes. 

#### Installation steps
1. Create a Kickstart file.
2. Publish the Kickstart file to the installer.
3. Boot Anaconda and point it to the Kickstart file. 

### Virtual Machines 
#### Configuring a RHEL Physical System as a Virtualization Host

- virt Yum module to prepare a system to become a virtualization host. 
  - `yum module list virt`

- Verify system requirements
  - `virt-host-validate`

#### Managing virtual machines with cockpit
Cockpit provides a web console interface for KVM management. 

- `yum install cockpit-machine`
  -If Cockpit is not already running, start and enable it.
    -`systemctl enable --now cockpit.socket`

## 13. Running containers

- podman, which directly manages containers and container images.
- skopeo, which you can use to inspect, copy, delete, and sign images. 
- buildah, which you can use to create new container images. (Not covered)

- Container naming convention: registry_name/user_name/image_name:tag
### Installing Container Management Tools
- `yum module install container-tools`

- Login to container registry `podman login registry.lab.example.com`
- Pull image: `podman pull registry.access.redhat.com/ubi8/ubi:latest`
- List images: `podman images`
- Inspect locally stored image `podman inspect ex:registry.redhat.io/rhel8/python-36`
- Run a container from image `podman run -it registry.access.redhat.com/ubi8/ubi:latest` (Pulls image if by fully qualified name if it doesn't exist)
- List running containers: `podman ps` + `-a` for all 
- Stop a container by SIGTERM: `podman stop containerName` after 10sec it uses SIGKILL
- Restart a container using same config: `podman restart`
- Start a process inside a running container: `podman exec containerID` or containerName
  - To replace the last saved container id or name `-l`
- Remove container from host: `podman rm containerName`
- Remove locally stored image: `podman rmi ex:registry.redhat.io/rhel8/python-36`
- Kill container main process: `podman kill contName` + `-s SIGKILL` to specify the signal
- List container port mappings: `podman port -a` You could specify container name or ID instead of all (-a)
- Search container registries for a container `podman search ex:registry.redhat.io/rhel8`
  - `--no-trunc` from longer image description
  - `--limit <number>` 	Limits the number of listed images per registry.
  - `--filter <filter=value>` 	Filters output based on conditions provided. Supported filters include:
  - `stars=<number>` Show only images with at least this number of stars.
  - `is-automated=<true|false>` Show only images automatically built.
  - `is-official=<true|false>` Show only images flagged as official. 
  - `-tls-verify <true|false>` 	Enables or disables HTTPS certificate validation for all used registries. Default=true

- `--name example-container-name` name a container
- `-a` to specify all containers
- `-it` allocate a terminal to the container 
- `--rm` run a container without interacting with it & remove once completed
- `-t -tty` allocates pseudo terminal 
- `-i --interactive`
- `-d --detach` run container in background
- `-d -p hostPort:containerPort` run container in background, maps host port to container port (Use root access for mapping host ports below 1024)
- `-e` to specify environment variables (before each variable)

### Configuring container registries
- System wide: `/etc/containers/registries.conf` 
- Rootless user: `$HOME/.config/containers/registries.conf`

- `podman info` Display configuration information & configured registries

- `skopeo inspect docker://registry.redhat.io/rhel8/python-36` Inspect a docker image

### Attaching Persistent Storage to a Container
- Configuring the ownership and permissions of the directory.
- Setting the appropriate SELinux context. `container_file_t`

#### Mounting a volume
- After `podman run` use `-v --volume` as `--volume host_dir:container_dir:Z`, the Z option applies SELinux container_file_t context

#### Managing containers as a service
- For rootless users: service start when that user opens first session and terminates with last session.
To disable this behavior:
  - `loginctl enable-linger` Start rootless services with server automatically
  - `loginctl disable-linger` Disable it
  - `loginctl show-user user` View current status

#### Create systemd user service
  -**Don't use podman commands to start, stop these containers**
  - **To manage systemd service with a user account, You must login directly with the specified user without su or sudo**
  - `/etc/systemd/system` system unit files location
  - `~/.config/systemd/user` user-based unit files location
  - `systemctl --user daemon-reload` --user is fixed
  - `systemctl --user enable myapp.service` Enable container to start when host machine starts
  - `systemctl --user start myapp.service`
  - `systemctl --user stop myapp.service`
  - `systemctl --user status myapp.service`

Create a unit file for existing container
  1. `podman generate systemd --name contName --files --new`
  2. Stop and delete the container, because systemd expects it to be absent
    - `--name container_name` specifies the name of an existing container
    - `--files` generates the unit file in the current directory.
    - `--new` configure the systemd service to create the continer when the service starts and delete it when the service stops.
  
