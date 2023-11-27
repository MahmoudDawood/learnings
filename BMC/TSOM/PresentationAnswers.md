# TSOM Presentation Answers
## 1. 
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
    - Custom traps defined by manfactureres and IT vendors.

## 3. PATROL agent vs ITDA agent
## 4. 
## 5. 
## 6. 
## 7. 
## 8. 
## 9. 