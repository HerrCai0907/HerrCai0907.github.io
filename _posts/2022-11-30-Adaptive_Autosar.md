---
title: Adaptive Autosar
tags: Autosar
---

# Adaptive Autosar

## Arch

### ara:exec Execution Management

1. is the first AP process launched by the OS
2. launches FCs and AA

### ara::sm State Management

1. change of machine state - restart / shutdown / sleep ...
2. change of function group state
3. activation and deactivation of partial networks
4. state dependent project specific actions
5. safety and security relevant functional cluster
6. brain of Adaptive Autosar

### ara::com Communication Management

1. backbone of the adaptive platform
2. responsible for communication between applications
3. SOME/IP, DDS, IPC and signal PDU
4. service discovery and service registry
5. language binding enable application developers to use services
6. network binding enables visualization of service's data on a network
7. generator create skeleton & proxies form meta model

End-to-End Protection

- CRC
- Data ID
  against communication faults:
- Corruption of info
- incorrect sequence
- masquerading

### ara::diag Diagnostics

1. DM realizes USDonIP
2. DoIp is a transport layer protocol for off-board communication
3. UDS for vehicle repairs
4. event memory manages DTCs

### ara::per Persistency

1. mechanism to store info in non-volatile memory
2. ensure data integrity and security
3. storage locations: File Storage / Key-Value Storage

### ara:tsync Time Synchronization

1. time synchronization between applications and ECUs
2. applications can get time info through the API
3. Time Sync contributors:
   1. StbM of class Platform
   2. std::chrono
   3. posix time

### ara::nm Network Management

1. if a node is not needed it goes to sleep
2. a node can sleep when it stops sending and receiving NM messages
3. coordination between state management and network management
4. there are active and passive network management nodes

### ara::ucm Update and Configuration Management

1. update the software in a safe and secure way in adaptive platform
2. data transfer over ara::com
3. can receive data from multiple clients
4. software package is an input unit of installation
5. vehicle package is assembled by OEM backend

- Vehicle Package:
  - OEM Authentication tag
  - Signed container
    - Software Package manifest A
    - Software Package manifest B
    - ...
    - Vehicle Package manifest
- Software Package A
  - Authentication tag
  - signed container
    - Executables
    - Data
    - manifests
    - Software Package manifest
- ...

process the package -> revert or finish processing -> revert or activate -> verify -> activated or rollback -> clean up

### ara::iam Identity and Access Management

1. control the application access to resources / APIs
2. capabilities of applications meet access control policy
3. system resources CPU / RAM out of scope
4. deployed applications will be cryptographically signed

### ara::crypto

1. API for cryptographic operation, key management, certificate handling
2. protect sessions for protocols such as TLS / SecOS
3. Platform level task executed vy crypto service manager
4. crypto driver response to store and execute

### ara::log

Log info of different severity
LT protocol standardized

### ara::phm Platform Health Management

Supervises applications and reports failures to SM

- Alive supervision
- DDL supervision
- Logical supervision
- Health channel supervision

### ara::core

Defines common functionalities for public APIs of functional clusters

Error handling with using C++ exceptions

- ara::core::ErrorCode
- ara::core::Result

Global Platform functions

- ara::core::initialize
- ara::core::deinitialize

### ara::idsm Intrusion Detection System manager

### ara::fw Firewall

1. filter ethernet traffic based on firewall rules
2. functional cluster manages the rules and configures the firewall engine
3. the rules are configured in the machine manifest / arxml
4. raise SEvs to IdsM

## COM

### Binding

Language Binding : Data Type -> Code
ara::com Binding : ara::com API -> transport technology

for Proxy / Skeleton Architecture: Service Interface Definition will generate Service Proxy (for consumer) / Service Skelton (for producer)

## Methodology & Manifest

1. Application Design: design-related aspects
2. Execution Manifest: executable code
3. Service Instance Manifest: executable SOC
4. Machine Manifest: Machine / Deployment related configuration
