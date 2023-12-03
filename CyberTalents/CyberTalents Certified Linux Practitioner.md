# CyberTalents Certified Linux Practitioner
## Linux Basics Commands 
- `whoami` current user
- `id` UID, GID, groups current user is in.
- `hostname` machine hostname
- `uname -a` OS kernel version and architecture.
- `pwd` or `echo $PWD`
- `cd -` Go to previous working dir

## File System
![file-system-overview](https://www.linuxfoundation.org/hubfs/Imported_Blog_Media/standard-unix-filesystem-hierarchy-1500x826.png)

## Text Operations
- `sort`
- `uniq` filters repeated lines in a file
- `tr` translating or deleting characters.
- `cut` Cutting sections from each line of a file.
- `awk opts 'selection-criteria {action}' file` write tiny program in a statement form.

## Users and groups
- `/etc/shadow` info about users' passwords
- `/etc/passwd` plain text-based db containing all users accounts.
- `/etc/group`
![/etc/passwd anatomy](https://website-cybertalents.s3.us-west-2.amazonaws.com/learn/linux/lessons/users-groups-1.png)
- `adduser USER` higher level than `useradd`
- `deluser USER`
- `delgroup GRP`
- `passwd USER` update user password
- `logout`
- `addgroup GROUP`
- `usermod -a -G GRP USR` append user to group
- `groups USR` show groups a user belongs to
- `gpasswd -d USR GRP` remove a user from a group.
- `visudo` give permissions, can be done through `/etc/sudoers.tmp`user
    - `which CMD` to find the location of a command.

## File Permissions
- `/etc/group` all groups defined in system
- `chmod nnn FILE` change access mode of a file or dir
    - `{ugoa}{+-=}{rwx}` symbolic way to set permissions.
- `chown USR:GROUP FILE` change ownership of a file or dir
- `chgrp GRP FILE` change only group ownership.

## Process Manipulation
- Process types
    1. Parent
    2. Child
    3. Orphan (assigned to init)
    4. Zombie
    5. Daemon
- Process states:
    1. Running or ready to run
    2. Waiting (Interruptible and uninterruptible)
    3. Stopped
    4. Zombie
- Process creation follow **Fork and execute** sequence, where it forks parent process, executes child on the forked process.
- `exec CMD` replaces parent with child process.
- `jobs` list running jobs
- `ps` list running process
    - `ax`
- `bg %n` move job to background, no n if we've only one
- `fg %n` move job to foreground, no n if we've only one
- `kill -SIGNUM PID`
    - `-l` list signals
    - 15: sigterm (default)
    - 2: sigint
    - 1: sighup
    - 9: sigkill

## Network Communications
- `ip` configure and analyze a network interface, to bring interfaces up or down, assign and remove addresses and routes, manage ARP cache, and much more.
    - `addr` or `a` view all the interfaces and the assigned ip address, subnet and default gateway
    - `ip addr show dev INTERFACE` display info about a single network interface
    - `if addr add IP dev INTERFACE` add IP address to a network interface on your machine or `del` to delete.
    - `ip link set INTERFACE {down, up}` take interface down or up.
- `ifconfig` display info about network interfaces.
- `netstat` detailed info about network activity and connectivity.
    - `-a` all tcp, udp connection
    - `-at` all tcp
    - `-l` active ports
    - `-u` all udp ports
- `ping HOSTNAME` check if host is reachable
- `curl URL` transfer data to and from a server
    - `-X METHOD` specify method (default GET)
    - `-o FILE URL` save response to a file
    - `-I` get only header
- `wget URL` non-interactive network downloader that is used to download files from the server even when the user has not logged on to the system