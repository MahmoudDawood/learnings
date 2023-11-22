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