# Port channel
Openconfig support for port-channel interface via openconfig-interfaces.yang
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
| 0.1 | 09/09/2019  |   Tejaswi Goel      | Initial version                   |

# About this Manual
This document provides information about the port channel configuration in SONiC using management framework container

# Scope
Covers Northbound interface for the Port channel feature, as well as Unit Test cases.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
|          LAG             | Link aggregation                    |
|          LACP            | Link Aggregation Control Protocol   |

# 1 Feature Overview
Add support for port channel create/set/get via CLI, REST and GNMI using  openconfig-interfaces.yang data model and sonic-mgmt-framework container

## 1.1 Requirements
Provide management framework capabilities to handle:
- Port channel creation and deletion
- Addition of ports to port channel
- Removal of ports from Port channel
- Configuration of min number of links in port channel
- Show all port channels summary
- Show individual port channel details

### 1.1.1 Functional Requirements
Provide management framework support to existing SONiC capabilities with respect to Port channel

### 1.1.2 Configuration and Management Requirements
- IS-CLI style configuration and show commands
- REST API support
- gNMI Support

Details described in Section 3.

### 1.1.3 Scalability Requirements
key scaling factors -N/A
### 1.1.4 Warm Boot Requirements
N/A

## 1.2 Design Overview
### 1.2.1 Basic Approach
Provide transformer methods in sonic-mgmt-framework container for port channel handling

### 1.2.2 Container
All code changes will be done in management-framework container

### 1.2.3 SAI Overview
N/A

# 2 Functionality
## 2.1 Target Deployment Use Cases
Manage/configure port channel interface via GNMI, REST and CLI interfaces
## 2.2 Functional Description
Provide CLI, GNMI and REST support port channel related commands handling

# 3 Design
## 3.1 Overview
Enhancing the management framework backed code and transformer methods to add support for port channel interface Handling

## 3.2 DB Changes
N/A
### 3.2.1 CONFIG DB
No changes to database schema's just populate Config DB
### 3.2.2 APP DB
Read data
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
### 3.3.2 Other Process
N/A

## 3.4 SyncD
N/A

## 3.5 SAI
N/A

## 3.6 User Interface
### 3.6.1 Data Models
List of yang models required for port channel interface management.
1. **openconfig-if-aggregate.yang** 
2. **openconfig-interfaces.yang**

Supported yang objects and attributes:
```diff

module: openconfig-interfaces
+   +--rw interfaces
+      +--rw interface* [name]
+         +--rw name                   -> ../config/name
+         +--rw config
          |  +--rw name?            string
          |  +--rw type             identityref
+         |  +--rw mtu?             uint16
          |  +--rw loopback-mode?   boolean
          |  +--rw description?     string
+         |  +--rw enabled?         boolean
          |  +--rw oc-vlan:tpid?    identityref
+         +--ro state
+         |  +--ro name?            string
          |  +--ro type             identityref
+         |  +--ro mtu?             uint16
          |  +--ro loopback-mode?   boolean
          |  +--ro description?     string
+         |  +--ro enabled?         boolean
          |  +--ro ifindex?         uint32
+         |  +--ro admin-status     enumeration
+         |  +--ro oper-status      enumeration
          |  +--ro last-change?     oc-types:timeticks64
          |  +--ro logical?         boolean
+         +--rw subinterfaces
+         |  +--rw subinterface* [index]
+         |     +--rw index           -> ../config/index
          |     +--rw config
          |     |  +--rw index?         uint32
          |     |  +--rw description?   string
          |     |  +--rw enabled?       boolean
          |     +--ro state
          |     |  +--ro index?          uint32
          |     |  +--ro description?    string
          |     |  +--ro enabled?        boolean
          |     |  +--ro name?           string
          |     |  +--ro ifindex?        uint32
          |     |  +--ro admin-status    enumeration
          |     |  +--ro oper-status     enumeration
          |     |  +--ro last-change?    oc-types:timeticks64
          |     |  +--ro logical?        boolean
          |     |  +--ro counters
          |     |     +--ro in-octets?             oc-yang:counter64
          |     |     +--ro in-pkts?               oc-yang:counter64
          |     |     +--ro in-unicast-pkts?       oc-yang:counter64
          |     |     +--ro in-broadcast-pkts?     oc-yang:counter64
          |     |     +--ro in-multicast-pkts?     oc-yang:counter64
          |     |     +--ro in-discards?           oc-yang:counter64
          |     |     +--ro in-errors?             oc-yang:counter64
          |     |     +--ro in-unknown-protos?     oc-yang:counter64
          |     |     +--ro in-fcs-errors?         oc-yang:counter64
          |     |     +--ro out-octets?            oc-yang:counter64
          |     |     +--ro out-pkts?              oc-yang:counter64
          |     |     +--ro out-unicast-pkts?      oc-yang:counter64
          |     |     +--ro out-broadcast-pkts?    oc-yang:counter64
          |     |     +--ro out-multicast-pkts?    oc-yang:counter64
          |     |     +--ro out-discards?          oc-yang:counter64
          |     |     +--ro out-errors?            oc-yang:counter64
          |     |     +--ro carrier-transitions?   oc-yang:counter64
          |     |     +--ro last-clear?            oc-types:timeticks64
          |     +--rw oc-vlan:vlan
          |     |  +--rw oc-vlan:config
          |     |  |  +--rw oc-vlan:vlan-id?   union
          |     |  +--ro oc-vlan:state
          |     |     +--ro oc-vlan:vlan-id?   union
+         |     +--rw oc-ip:ipv4
+         |     |  +--rw oc-ip:addresses
+         |     |  |  +--rw oc-ip:address* [ip]
+         |     |  |     +--rw oc-ip:ip        -> ../config/ip
+         |     |  |     +--rw oc-ip:config
+         |     |  |     |  +--rw oc-ip:ip?              oc-inet:ipv4-address
+         |     |  |     |  +--rw oc-ip:prefix-length?   uint8
+         |     |  |     +--ro oc-ip:state
+         |     |  |     |  +--ro oc-ip:ip?              oc-inet:ipv4-address
+         |     |  |     |  +--ro oc-ip:prefix-length?   uint8
          |     |  |     |  +--ro oc-ip:origin?          ip-address-origin
+         |     +--rw oc-ip:ipv6
+         |        +--rw oc-ip:addresses
+         |        |  +--rw oc-ip:address* [ip]
+         |        |     +--rw oc-ip:ip        -> ../config/ip
+         |        |     +--rw oc-ip:config
+         |        |     |  +--rw oc-ip:ip?              oc-inet:ipv6-address
+         |        |     |  +--rw oc-ip:prefix-length    uint8
+         |        |     +--ro oc-ip:state
+         |        |     |  +--ro oc-ip:ip?              oc-inet:ipv6-address
+         |        |     |  +--ro oc-ip:prefix-length    uint8
          |        |     |  +--ro oc-ip:origin?          ip-address-origin
          |        |     |  +--ro oc-ip:status?          enumeration
+         +--rw oc-eth:ethernet
+         |  +--rw oc-eth:config
          |  |  +--rw oc-eth:mac-address?           oc-yang:mac-address
          |  |  +--rw oc-eth:auto-negotiate?        boolean
          |  |  +--rw oc-eth:duplex-mode?           enumeration
          |  |  +--rw oc-eth:port-speed?            identityref
          |  |  +--rw oc-eth:enable-flow-control?   boolean
+         |  |  +--rw oc-lag:aggregate-id?          -> /oc-if:interfaces/interface/name
+         |  +--ro oc-eth:state
          |  |  +--ro oc-eth:mac-address?              oc-yang:mac-address
          |  |  +--ro oc-eth:auto-negotiate?           boolean
          |  |  +--ro oc-eth:duplex-mode?              enumeration
          |  |  +--ro oc-eth:port-speed?               identityref
          |  |  +--ro oc-eth:enable-flow-control?      boolean
          |  |  +--ro oc-eth:hw-mac-address?           oc-yang:mac-address
          |  |  +--ro oc-eth:negotiated-duplex-mode?   enumeration
          |  |  +--ro oc-eth:negotiated-port-speed?    identityref
          |  |  +--ro oc-eth:counters
          |  |  |  +--ro oc-eth:in-mac-control-frames?    oc-yang:counter64
          |  |  |  +--ro oc-eth:in-mac-pause-frames?      oc-yang:counter64
          |  |  |  +--ro oc-eth:in-oversize-frames?       oc-yang:counter64
          |  |  |  +--ro oc-eth:in-undersize-frames?      oc-yang:counter64
          |  |  |  +--ro oc-eth:in-jabber-frames?         oc-yang:counter64
          |  |  |  +--ro oc-eth:in-fragment-frames?       oc-yang:counter64
          |  |  |  +--ro oc-eth:in-8021q-frames?          oc-yang:counter64
          |  |  |  +--ro oc-eth:in-crc-errors?            oc-yang:counter64
          |  |  |  +--ro oc-eth:in-block-errors?          oc-yang:counter64
          |  |  |  +--ro oc-eth:out-mac-control-frames?   oc-yang:counter64
          |  |  |  +--ro oc-eth:out-mac-pause-frames?     oc-yang:counter64
          |  |  |  +--ro oc-eth:out-8021q-frames?         oc-yang:counter64
+         |  |  +--ro oc-lag:aggregate-id?             -> /oc-if:interfaces/interface/name
+         +--rw oc-lag:aggregation
+         |  +--rw oc-lag:config
          |  |  +--rw oc-lag:lag-type?    aggregation-type
+         |  |  +--rw oc-lag:min-links?   uint16
+         |  +--ro oc-lag:state
          |  |  +--ro oc-lag:lag-type?    aggregation-type
+         |  |  +--ro oc-lag:min-links?   uint16
          |  |  +--ro oc-lag:lag-speed?   uint32
+         |  |  +--ro oc-lag:member*      oc-if:base-interface-ref
```

**Note:** Currently LACP fallback not supported in Openconfig data model

### 3.6.2 CLI
#### 3.6.2.1 Configuration Commands
| Command description | CLI command |
| :------ | :----- |
| Create a port-channel  | sonic(config)# interface port-channel \<channel-number>  |
| Configure the number of links <br>default 1 |  sonic(conf-if-poX)# minimum-links \<number> |
| Add port member | sonic(config)# interface EthernetX \<id> <br><br> sonic(conf-if-EthernetX)# channel-group \<channel-number>  |
| Remove a port member | sonic(config)# interface EthernetX <br><br> sonic(conf-if-EthernetX)# no channel-group|
| Delete a port channel | sonic(config)# no interface port-channel \<channel-number> |

#### 3.6.2.2 Show Commands
| Command description | CLI command |
| :------ | :----- |
| Display summary information about port channels| sonic# show port-channel summary |

#### 3.6.2.3 Debug Commands
N/A

#### 3.6.2.4 IS-CLI Compliance
N/A

### 3.6.3 REST API Support
| Command description | OpenConfig Command Path |
| :------ | :----- |
| Create/Delete a port-channel | /openconfig-interfaces:interfaces/interface={name} |
| Set the number of links in a LAG <br> default 1 | /openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/config/min-links |
| Add/Remove port member | /openconfig-interfaces:interfaces/interface={name}/openconfig-if-ethernet:ethernet/config/openconfig-if-aggregate:aggregate-id  |
| Get Port channel details | /openconfig-interfaces:interfaces/interface={name}/state/[admin-status | mtu] <br><br> openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/state/[min-links|member] |

# 4 Flow Diagrams
N/A

# 5 Error Handling
TBD

# 6 Serviceability and Debug
TBD

# 7 Warm Boot Support
N/A

# 8 Scalability
N/A

# 9 Unit Test
- Validate port channel creation via CLI, GNMI and REST
- Validate min links config for port channel via CLI, GNMI and REST
- Validate addition of ports to Port channel via CLI, GNMI and REST
    - Check if error returned if port already part of other port-channel or VLAN
    - Validate MTU, speed and list of Vlans permitted on each member link are same as port-channel
- Validate removal of ports from Port channel via CLI, GNMI and REST
- Validate port channel deletion via CLI, GNMI and REST

# 10 Internal Design Information
Internal BRCM information to be removed before sharing with the community.

