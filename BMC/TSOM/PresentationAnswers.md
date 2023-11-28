# TSOM Presentation Answers
## 1. How KM is sent to each agent?
- To use the KM *deployment* feature, you need PATROL Agents version 10.0 and later.
- PATROL agent loads specified KMs at start-up
- KMs are deployed centrally and downloaded from the TS console (Can be loaded form [Operator console or Patrol developer console](https://docs.bmc.com/docs/PATROLAgent/11302/loading-knowledge-modules-by-console-and-agent-828956275.html)).
- BMC provides [out of the box Knowledge Modules](https://docs.bmc.com/docs/TSOperations/11304/list-of-monitoring-solutions-and-kms-in-infrastructure-management-937357354.html) (KMs) for monitoring of operating systems, virtualization infrastructure, business applications, databases, and hardware devices. You can develop [custom KMs](https://docs.bmc.com/docs/PATROLAgent/11302/developing-a-patrol-knowledge-module-828956351.html) and extend the monitoring capabilities of PATROL Agent.
- PATROL Agent consists of:
  - Perform Agent executables (BDS_SDService.exe, which is registered as a service on Windows platforms, and bgscollect.exe ) 
  - PATROL Agent executable (patrolagent.exe ), which is registered as a service. 
  - ![architecture](https://docs.bmc.com/docs/PATROLAgent/2002/files/913226678/913226719/1/1490996934523/PATROL_3_architecture1.png)
- Patrol agents has support for clusters and failovers.
- Patrol agent is is included in the base repository, **but** since v11.3.02 and before it could be installed [manually](https://docs.bmc.com/docs/PATROLAgent/11302/installing-828956013.html)
- KM: Defines behavior of agent
- KB: Defines behavior of IM cell
  - [Cell](https://docs.bmc.com/docs/TSInfrastructure11304/infrastructure-management-cells-and-knowledge-base-937358791.html):
    - Server cell: A part of TSIM providing local events.
    - Event management cell (Remote cell): functions as part of a larger distributed network of cells that propagate events to the Server Cell. If **service impact management** is implemented, a Remote Cell also associates events with service model components and calculates the components’ statuses. If service impact management is not implemented, then the cell is simply an event management cell.
- When you distribute your PATROL KM, you will need a **PATROL Knowledge Module List** (KML) file. PATROL KMs are loaded into the PATROL Developer Console using a KML (.kml ) file. The KML file is a list of all Knowledge Module (.km ) files that are contained in the PATROL Knowledge Module.
  - ```
    !PATROLV3.0.5   C6B9AABB0058948D
    !++
    !
    ! PATROL Session Knowledge Module
    !
    !--
    ! Metadata for BPPM-CMA
    !
    ! MetaKMLDisplayName = "Top-N Process Monitor"
    ! MetaKMLDescription = "Configure Top-N Process Monitoring"
    !
    KM_LIST = {
                  "TOPPROC_MONITOR.km"
                  "TOPPROC_MONITOR_GROUP.km"
                  "TOPPROC_MONITOR_TIME_GROUP.km"
                  "TOPPROC_PROC.km"
    }
    ! 3368  


## 2. SNMP Trap:
![SNMP](https://cdn.comparitech.com/wp-content/uploads/2018/09/What-is-an-SNMP-Trap_.jpg)
- Simple Network Management Protocol for network monitoring
- Consists of: Manger devices, Managed devices, SNMP agent running on managed devices, SNMP software NMS running on manager (Network Management System)
- Is a protocol data unit (PDU). Unlike other PDU types, with an SNMP **trap**, an agent can send an unrequested message to the manager to notify about critical events regarding object in the managed device.
- Why: It can be overwhelming for managers to request information from every device, it would impact network performance.
- Using UDP can make it unreliable, but monitoring tools help collect, identify, and have alerts sent.
- SNMP Pooling: Monitoring software asks device for it’s status/health usually every 5  min.
- Has 2 types
  1. Generic (or Standard) traps:
    - six standard traps defined in RFC 1215 of Internet Engineering Task Force: coldStart, warmStart, linkDown, linkUp, authenticationFailure, and egpNeighborLoss.
  2. Enterprise-specific traps
    - Custom traps defined by manufacturers and IT vendors.

## 3. PATROL agent vs ITDA agent
- PATROL: A monitoring and management framework within TSOM. It's a component designed for monitoring diverse IT environments, collecting data, and managing applications, and networks.
-  [PATROL for IT Data Analytics](https://docs.bmc.com/docs/ITDA/113/agent-types-766663765.html#Agenttypes-SupportedAgenttypes) provides: 
  - Collection station
  - Collection agent.


- PATROL Agent: 

## 4. Remote action vs Remote monitoring
- Remote action: Performing task or executing operation on a remote device or system. ex: remote access (Can only be triggered from IM in TSOM)
- Remote monitoring: Observing, tracking and collection data from a remote device. ex: Network monitoring (Can be done by agent)

## 5. xCmd:
Protocol allows executing **multiple commands simultainoulsy** on a remote Windows-based system without installing any client software.
- Execute internal commands of OS like 'dir' directly.
- Pre-requisites
  - Have administrative privileges to copy xCmd and run it as a service on the remote computer.
  - Adapters like Windows command, Active directory, PowerShell and only works on peers running a windows-based system.
- Execution step from [docs](https://docs.bmc.com/docs/AtriumOrchestratorContent/201603/xcmd-utility-667807366.html)
- SSH: 22, telnet: 23
- PATROL agent **port number is set on package creation** -Default values sets event destination to localhost/1828.
- A [list](https://docs.bmc.com/docs/TSOperations/113/communication-ports-and-protocols-843619816.html) of TSOM communication ports.

## 6. Remote installation and auto scaling
Remote installation in BMC TSOM was not mentioned once.
- Installation steps:
  - Repository >> Deployable package >> Login to the host >> Download it from there >> Extract >> Install.
- We can do remote server management and software installation/updates using various DevOps and configuration management tools like:
  1. Ansible
  2. Puppet
  3. Chef
  4. SaltStack
  5. Terraform
- Patrol agents has support for [clusters and failovers](https://docs.bmc.com/docs/PATROLAgent/2002/support-for-clusters-and-failovers-913225597.html).

## Additional points
### Famous ports
- HTTP – Port 80
- HTTPS – 443
- FTP – 21
- FTPS / SSH – 22
- SMTP – 25 (Alternate: 26)
- SMTP SSL – 587
- MySQL – 3306
- PostgreSQL – 5432

### TSIM separate console
- Operator console (earlier than 11.3.04)
- TrueSight console (since 11.3.04)
- Didn't find any separate console for TSIM, although PATROL has it's own cli and patrol developer console.