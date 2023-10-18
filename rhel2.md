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

-------------------------------------------------------------
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
- `Minutes Hours Day of month Month Day of week Command  ` order
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
--------------------------------------------------------------
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

-------------------------------------------------------------
## 4. ACL

### ACL Permissions
- `ls -l reports.txt` list a minimal details, + sign refers to extended ACL
- `getfacl reports.txt`
- `setfacl RULE FILE` 
  - `-m` modify 
  - `-x` delete entry
  - `-R` recursive
  - `u:user:perm,g:group:perm`
  - `mask::rw-` masks all permission except owner user 
  - `--set-file=-` use a file to set acl, - means get it from input

-------------------------------------------------------------
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
- `semanage fcontext -a -t httpd_sys_content_t '/dir(/.*)?'` + `restorecon -Rv /dir` to ensure all files have to correct context

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

-------------------------------------------------------------
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

--------------------------------------------------------------
## 7. Logical volumes
*Physical Device >> Physical Volume >> Volume Group >> Logical Volume
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
- `pvdisplay /dev/..`
- `vgdisplay vgName`
- `lvdisplay /dev/..`
-------------------------------------------------------------
## 11. Network Security
### Configuring the firewall
- /etc/firewalld
- Web console
- firewall-cmd

|firewall-cmd commands                               |	Explanation|
|----------------------------------------------------|-------------|
| --get-default-zone 	| Query the current default zone.|
| --set-default-zone=ZONE | Set the default zone. This changes both the runtime and the permanent configuration.|
| --get-zones 	| List all available zones.|
|--get-active-zones 	|List all zones currently in use (have an interface or source tied to them), along with their interface and source information.
|--add-source=CIDR [--zone=ZONE] 	|Route all traffic coming from the IP address or network/netmask to the specified zone. If no --zone= option is provided, the default zone is used.
|--remove-source=CIDR [--zone=ZONE] 	|Remove the rule routing all traffic from the zone coming from the IP address or network/netmask network. If no --zone= option is provided, the default zone is used.
|--add-interface=INTERFACE [--zone=ZONE] 	|Route all traffic coming from INTERFACE to the specified zone. If no --zone= option is provided, the default zone is used.
|--change-interface=INTERFACE [--zone=ZONE] |	Associate the interface with ZONE instead of its current zone. If no --zone= option is provided, the default zone is used.
|--list-all [--zone=ZONE] 	|List all configured interfaces, sources, services, and ports for ZONE. If no --zone= option is provided, the default zone is used.
|--list-all-zones 	|Retrieve all information for all zones (interfaces, sources, ports, services).
|--add-service=SERVICE [--zone=ZONE] 	|Allow traffic to SERVICE. If no --zone= option is provided, the default zone is used.
|--add-port=PORT/PROTOCOL [--zone=ZONE] 	|Allow traffic to the PORT/PROTOCOL port(s). If no --zone= option is provided, the default zone is used.
|--remove-service=SERVICE [--zone=ZONE] 	|Remove SERVICE from the allowed list for the zone. If no --zone= option is provided, the default zone is used.
|--remove-port=PORT/PROTOCOL [--zone=ZONE] 	|Remove the PORT/PROTOCOL port(s) from the allowed list for the zone. If no --zone= option is provided, the default zone is used.|
|--reload 	|Drop the runtime configuration and apply the persistent configuration. |

ex
- firewall-cmd --permanent --add-port=82/tcp
  - firewall-cmd --reload (To activate the permanent setting)

### SELinux Port Labeling
- semanage port -l

- To add a port to an existing port label (type)
-  semanage port -a -t port_label -p tcp|udp PORTNUMBER
-                -d [delete]
-                -m (modify)

- To view local changes to the default policy
- semanage port -l -c

IN SELINUX >> sealert -a /var/log/audit/audit.log ....

## 12. Installing RHEL

### TMUX shortcuts 
press and release Ctrl+B, and then press the number key of the window you want to access. With tmux, you can also use Alt+Tab to rotate the current focus between the windows.
|Key sequence| 	contents|
|-----------|-------|
|Ctrl+Alt+F1 |	Access the tmux terminal multiplexer.
|Ctrl+B 1 	|When in tmux, access the main information page for the installation process.
|Ctrl+B 2 	|When in tmux, provide a root shell. Anaconda stores the installation log files in the /tmp directory.
|Ctrl+B 3 	|When in tmux, display the contents of the /tmp/anaconda.log file.
|Ctrl+B 4 	|When in tmux, display the contents of the /tmp/storage.log file.
|Ctrl+B 5 	|When in tmux, display the contents of the /tmp/program.log file.
|Ctrl+Alt+F6 |	Access the Anaconda graphical interface. 

### Automating RHEL Installation with Kickstart
* Located at /root/anaconda-ks.cfg or create by Kickstart generator website
* Ksvalidator package validates kickstart files

- % software
- @ package groups
- @^ environment groups (group or groups)
- %pre before disk partitioning
- %post after installation

#### Installation commands
- url --url="..." (URL Referring to installation media)
- repo --name="..." --baseurl=... (Additional package for installation, MUST be a valid yum repository)
- text: Forces a text mode install. 
- vnc: Allows the graphical installation to be viewed remotely over VNC. 

#### Partitioning commands
- clearpart: Removes partitions from the system prior to creation of new partitions. By default, no partitions are removed.
  - clearpart --all --drives=sda,sdb --initlabel

- part: Specifies the size, format, and name of a partition.
  - part /home --fstype=ext4 --label=homes --size=4096 --maxsize=8192 --grow

- autopart: Automatically creates a root partition, a swap partition, and an appropriate boot partition for the architecture. On large enough drives, this also creates a /home partition.

- ignoredisk: Controls Anaconda's access to disks attached to the system.
  - ignoredisk --drives=sdc

- bootloader: Defines where to install the bootloader.
  - bootloader --location=mbr --boot-drive=sda

- volgroup, logvol: Creates LVM volume groups and logical volumes.
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

- yum install cockpit-machine
  -If Cockpit is not already running, start and enable it.
    -`systemctl enable --now cockpit.socket`

-----------------------------------------
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
    - `--new` configure the systemd service to create the container when the service starts and delete it when the service stops.
  
