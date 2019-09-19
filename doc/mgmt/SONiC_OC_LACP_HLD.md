# LACP
Management Interfaces for Link aggregation control protocol

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 09/18/2019  |   Arthi Sivanantham      | Initial version                   |

# About this Manual

This document provides north bound interface details for Link aggregation control protocol feature.

# Scope

This document covers the "show" commands supported for Link aggregation control protocol feature based on openconfig-lacp yang and Unit test cases. It does not include the protocol design or protocol implementation details.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| LACP                      | Link aggregation control protocol  |

# 1 Feature Overview

Provide CLI, GNMI and REST management framework capabilities for GET support for the existing SONiC implemenatation of LACP feature.


## 1.1 Requirements

### 1.1.1 Functional Requirements

Provide "show" commands for:

1) LACP portchannel

2) Portchannel members


### 1.1.2 Configuration and Management Requirements

Supported "show" Commands via:
- IS-CLI style show commands
- REST API support
- gNMI Support

Configuration of LACP using management framework is **not supported** due to the following limitations:

- No support for LACP protocol-specific configurations in SONiC. For example, on creating port channels and adding members to it, “teammgrd” daemon (in SONIC code base):
1.	Reads user configuration from CONFIG DB
2.	Spawns an instance of libteamd (open source package for LACP protocol stack) with the given user configurations and a couple of default LACP options.

Now to support protocol specific configurations (LACP interval, LACP mode, Priority, MAC ) we would need to enhance:
1.	REDIS CONFIG DB PORTCHANNEL Table schema
2.	Code changes in “teammgrd” to read these protocol specific configs and to use “teamdctl” control utility to configure the above-mentioned configs to an already running "teamd" daemon.

- `teamd` supports only a few options (not LACP-specific) to be configured via `teamdctl` utility. This could be overcome by enhancing `teamd` to support configuration of LACP parameters.

Due to the above mentioned reasons, LACP **configuration is not supported** in the initial release.

### 1.1.3 Scalability Requirements
N/A

### 1.1.4 Warm Boot Requirements
N/A

## 1.2 Design Overview
### 1.2.1 Basic Approach
N/A


### 1.2.2 Container
Management framework container

### 1.2.3 SAI Overview
N/A

# 2 Functionality
## 2.1 Target Deployment Use Cases
N/A
## 2.2 Functional Description
N/A


# 3 Design
## 3.1 Overview
N/A
## 3.2 DB Changes
No changes to existing DBs and no new DB being added.
### 3.2.1 CONFIG DB
### 3.2.2 APP DB
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
N/A
### 3.3.1 Orchestration Agent
N/A
### 3.3.2 Other Process
N/A

## 3.4 SyncD
N/A

## 3.5 SAI
N/A

## 3.6 User Interface
### 3.6.1 Data Models

Using "openconfig-lacp" state containers for GET support.

```diff
module: openconfig-lacp
    +--rw lacp
       +--ro state
       |  +--ro system-priority?   uint16
       +--rw interfaces
          +--rw interface* [name]
             +--rw name       -> ../config/name
             +--ro state
             |  +--ro name?              oc-if:base-interface-ref
             |  +--ro interval?          lacp-period-type
             |  +--ro lacp-mode?         lacp-activity-type
             |  +--ro system-id-mac?     oc-yang:mac-address
             |  +--ro system-priority?   uint16
             +--ro members
                +--ro member* [interface]
                   +--ro interface    -> ../state/interface
                   +--ro state
                      +--ro interface?          oc-if:base-interface-ref
                      +--ro activity?           lacp-activity-type
                      +--ro timeout?            lacp-timeout-type
                      +--ro synchronization?    lacp-synchronization-type
                      +--ro aggregatable?       boolean
                      +--ro collecting?         boolean
                      +--ro distributing?       boolean
                      +--ro system-id?          oc-yang:mac-address
                      +--ro oper-key?           uint16
                      +--ro partner-id?         oc-yang:mac-address
                      +--ro partner-key?        uint16
                      +--ro port-num?           uint16
                      +--ro partner-port-num?   uint16
```


### 3.6.2 CLI
#### 3.6.2.1 Configuration Commands
N/A

#### 3.6.2.2 Show Commands

1) Show LACP system priority
```
   sonic# show lacp system-priority
```
2) Show LACP information about all EtherChannels
```
   sonic# show lacp port-channel
```
3) Show LACP information about a specific EtherChannel
```
   sonic# show lacp port-channel interface port-channel < port-channel id >
```

#### 3.6.2.3 Debug Commands
#### 3.6.2.4 IS-CLI Compliance
The following table maps SONIC CLI commands to corresponding IS-CLI commands. The compliance column identifies how the command comply to the IS-CLI syntax:

- **IS-CLI drop-in replace**  – meaning that it follows exactly the format of a pre-existing IS-CLI command.
- **IS-CLI-like**  – meaning that the exact format of the IS-CLI command could not be followed, but the command is similar to other commands for IS-CLI (e.g. IS-CLI may not offer the exact option, but the command can be positioned is a similar manner as others for the related feature).
- **SONIC** - meaning that no IS-CLI-like command could be found, so the command is derived specifically for SONIC.

|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|
|:---:|:-----------:|:------------------:|-----------------------------------|
|show lacp system-priority |IS-CLI-like  | show lacp system-identifier| https://www.cisco.com/c/m/en_us/techdoc/dc/reference/cli/nxos/commands/l2/show-lacp.html |
|show lacp port-channel |IS-CLI drop-in replace |show lacp port-channel | https://www.cisco.com/c/m/en_us/techdoc/dc/reference/cli/nxos/commands/l2/show-lacp.html|
|show lacp port-channel interface port-channel < portchannel id > | IS-CLI drop-in replace|show lacp interface port-channel < portchannel number > | https://www.cisco.com/c/m/en_us/techdoc/dc/reference/cli/nxos/commands/l2/show-lacp.html|
| | | | |
| | | | |
| | | | |
| | | | |

**Deviations from IS-CLI:**

`show lacp system-priority` is IS-CLI-like because in openconfig-lacp yang, there's no leaf/attribute for global MAC address.


### 3.6.3 REST API Support
**GET**

GET is supported using the following openconfig lacp yang objects.

- `/openconfig-lacp:lacp/state/system-priority`
- `/openconfig-lacp:lacp/interfaces`
- `/openconfig-lacp:lacp/interfaces/interface={name}/state`

# 4 Flow Diagrams
N/A

# 5 Error Handling


# 6 Serviceability and Debug


# 7 Warm Boot Support
N/A

# 8 Scalability
N/A

# 9 Unit Test
The following lists the unit test cases added for the north bound interfaces for LACP:

- `show lacp system-priority` should show the default global priority used on the box.
- `show lacp port-channel` should display information about portchannel name, interval, mode, priority for all portchannels
- `show lacp interface port-channel < port-channel id >` should display the above mentioned information for the specified port-channel.

# 10 Internal Design Information
N/A
