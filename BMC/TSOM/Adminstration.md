# Adminstration
## RBAC
Restrict access to data and operations
### Remedy Single Sign On RSSO
- Configure Remedy SSO
- Import/export rsso configuration
- Configure realm for different auth types
- Create/delete/modify realms, users and groups
- Map users to groups
- Change admin user and password
- Define cookie domain, session timeout and ongoing sessions.


- **Removing a user from group or deleting it from RSSO**
    - His polices are disabled after 30 mins
    - Configuration is removed from affected PATROL agents, instances are marked for deletion
    - Dashboards created by him are deleted within 30 mins.
- Check for and remove associations from monitoring policies and authorization profiles, consider viewing shared dashboards.

- Group cannot belong to two different realms.
 
#### RBAC Workflow
1. Define users, role, groups per env
2. Create realms, import users and groups
3. Add/modify components the compromise authorization profile
4. Create new authorization profile

## Module 3: Monitoring Infrastructure
### TSIM: Self Monitoring
#### Monitoring solution components
- BMC PATROL Agent: Core of TSIM 
    - Self tuning
    - Self configuring: based on KB
    - Resource efficient
    - Uses caching since v9.6 or 10
- BMC PATROL Knowledge Modules (KMs): Instructions to PATROL Agent.

#### Repository 
Includes current versions of PATROL
- Must download or copy repo files to TS Presentaion Server locally.
- Consists of base & extended files.

#### Creating a monitoring solution
Select component to include in deployable package
    - To be used by agent (needs an already created agent account) on the host.
- Managed by TS console -previously was PATROL console for local agents & PCM (Patrol Configuration Manager), but not the best for enterprise level-
- No. of total installed components = 84
- IntegraionService variable: PROTOCOL:TRGT-NAME-OR-IP:PORT
- Check everything was setup right in the last creation step.
- Deployed packages are stored in TS DB, accessed and downloaded, unzipped, installed from anywhere.
- Check installed packages from services window -restart it- 
    - `/AgentSetup/textParameterLength` limit the length of data sent by the PATROL Agent.
- Versions prerequists:
    - PATROL Agent >= 10.0
    - Integration service >= 10.5
- Deploy package to agent from Managed Devices tab.
#### Policy
Configure monitoring solution on agents, creates servers and agent thresholds, sets polling intervals, monitoring, data or event collection or both, polling interval, thresholds, and where it's filtered.
- Managed from TS Console only.
- Monitoring policy is mandatory, staging and blackout are not.
- Decides everything about 
- Blackout policy uses configured time frames.
- Supports instace supression from being forwarded to TSIM, but can still be forwarded to TS Console by mapping them to the associated 'Device' level.

## TSOM Presentation (Temp)
The product components that you can download and install are determined by the entitlements of your TrueSight Operations Management license. 

- Atrium SSO => RSSO @TS v11.0
    - Must be installed and completely configured before installing the rest of components or upgrading existing TSOM, must by installed on it's own server and ready to go first.
    - Internal users and groups can be migrated, LDAP/AD users can't.
    - TSPS upgrade to v11 supports a seamless switch, just configure it like before.
    - Supports HA
    - Can upgrade onething at a time, ex: TSIM: v10.7 and 10.5 are supported with TSPS v11 and RSSO.
    - RSSO from 9.1.03.001 are version compatible with TS.
    - Requirements:
        - Browsers compatible versions
        - Tomcat security recommendations.
        - DB: see reqs 
    - Components architecture.
        - Agent:
            - Protects resources by auth, redirects unauth reqs to SSO server web app.
            - Define users by domain
            - Controls used RSSO server in multi.


## Documentation
### TSOM
TrueSight Operations Management enables IT departments to monitor performance and availability of the infrastructure and applications that comprise their services, monitor events coming from the infrastructure devices, and monitor transactions from the applications that enable the services they deliver.
- Features
    - Monitoring:	IT Operations personnel use the monitoring views to monitor the infrastructure and application health.
    - Dashboard: Executives: and IT Operations personnel use dashboards to monitor the environment from a specialized view, such as a set of applications or performance graphs related to a critical offering or customer.
    - Configuration:	Solution Administrators and specialists configure the data collected from the component products and the applications monitored.
    - Administration:	Solution Administrators configure the component products and integrations to the Presentation Server. The administration functions also include role-based access control to data and features.

### TSPS
TrueSight Presentation Server hosts the web-based TrueSight console functions and consumes data from various TrueSight products to provide a consolidated set of views for monitoring the infrastructure, real and synthetic applications, and capacity planning. In addition to data presentation, the TrueSight Presentation Server performs functions such as role-based access control and data management functions such as storage and persistence.

### RSSO
- Configure usage of authenticated types:
    - Install the Remedy Single Sign-On server and configure it with an authentication server such as LDAP/AD or SAMLv2.
    - Create the required tenants (realms), user groups, and users on the Remedy Single Sign-On Admin console.
    - Integrate the Remedy Single Sign-On server with the TrueSight Presentation Server during the TrueSight Presentation Server installation or upgrade.

## TSOM Architecture
#### TSIM
enables event management, service impact management, performance monitoring, and performance analytics.

Infrastructure Management interfaces:	TrueSight Infrastructure Management provides additional interfaces for monitoring the data collected and processed by this system and for configuring and customizing the system.
	
Infrastructure Management Server:	The Infrastructure Management Server receives events and data from the following sources:

    PATROL Agents
    Event Management Cells
    Third-party event and data sources
After the Infrastructure Management Server collects this information, it processes events and data using a powerful analytics engine and additional event processing instructions stored in its database. The Infrastructure Management Server can also leverage a service model (built within the Infrastructure Management Server or published from the Atrium CMDB) to map data and events in context with business services. You can deploy one or more Infrastructure Management servers. 

Integration Service hosts: The Integration Service manages events from event sources such as PATROL Agents, event adapters, and SNMP traps, and forwards performance data to the Infrastructure Management Server. 	Infrastructure Management Integration Service Open link

Event and performance data: Events are collected from the following sources:
    PATROL – PATROL Agents can be configured to generate events about metrics. As a best practice, the PATROL Agent is configured to generate nonperformance, availability-related events, binary events, and events from the Knowledge Module (KM).
    TrueSight Infrastructure Management Server – The TrueSight Infrastructure Management Server can be configured to generate performance-related events.

    Adapters – Events from other sources can be processed by event adapters such as SNMP adapters and log file adapters. All events from various sources are sent to event processing cells for correlation, deduplication, filtering, normalization, and enrichment.

Performance data is collected from PATROL Agents or from other sources such as BMC Portal and Microsoft System Center Operations Manager, using the appropriate adapters. Not all performance data has to be forwarded to the Infrastructure Management Server. Performance data can be collected and stored at the PATROL Agents and visualized as trends in the Infrastructure Management console without streaming the data to the Infrastructure Management Server.
	