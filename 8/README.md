---
domain: rfc.tango-controls.org
shortname: 8/SERVER
name: Device Server Model
status: raw
editor: 
contributors: Piotr Goryl, Vincent Hardion
---

This document provides the specification of the Device Server.

See also: 2/Device


## Preamble

Copyright (c) 2019 Tango Community.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).


## Tango Device Server specification

 This specification formally document the Tango Device Server model. 
 Runtime implementation of the Device Server model in conforming Tango implementations MUST follow this specification.

### Goals

 In a Tango Control System a Device Server is in charge of managing the life cycle and the communication protocol of one or several Tango Devices.
 The Device Server can also offer different services to the clients as well as for the Device.

Additionally, it aims to:

* Provide a blackbox that registers the operations executed on every Device

* Be usable as a Device

### Use Cases

The main use cases for Device Server are:

* to manage several Devices in the same process. 

* to share the same channel of communication

* to provide the connection protocols for accessing the managed Device

* to provide different services like logging

* to serialise the operations per Device

* to sign up or sign out to the central registry 



## Specification

The Device Server manages the communication with one or several Devices.  Additionally it enforces the compatibility of behaviour with the Tango Control System and offers different meta services.

The Device Server SHALL implement the following specifications to control and monitor the Device:

* A Device Server represents the process which manages one or several Devices

* A Device Server SHALL manage at least one Device. 

* A Device Server MAY manage Devices belonging to different Classes

* A Device MUST NOT be managed by more than one Device Server

* A Device Server instance reference SHALL be unique in a Tango Control system

* The communication to the Device Server SHALL be done through a Tango Device so called Administration Device or Admin Device .


The specification introduces as well the definition of the processes which are used for the Administration Device Name:

* A Server is a type of program which execute a Device Server

* An Instance is an unique identifier as several processes of the same Server MAY exist in a Tango Control System

* A Server Instance is finally what identify the actual process in the Tango Control System

* A Server Instance CAN only execute one Device Server


The name of the Administration Device SHALL follow this convention:
```ABNF
administration-device-name = dserver "/" server "/" instance
dserver = "dserver" ; dserver is a reserved domain following the device-name convention.
server = family
instance = member
```

* The member part of the Administration Device Name SHALL be the Instance

* The family part of the Administration Device Name SHALL be the Server name


#### Device Server interface


| Property Name | description |
|---|---|
| polling_threads_pool_conf | TODO |

| Command Name | description |
|---|---|
| AddLoggingTarget | DevVarStringArray | DevVoid |
| AddObjPolling | DevVarLongStringArray | DevVoid |
| DevLockStatus | DevString | DevVarLongStringArray |
| DevPollStatus | DevString | DevVarStringArray |
| DevRestart | DevString | DevVoid |
| EventConfirmSubscription | DevVarStringArray | DevVoid |
| EventSubscriptionChange | DevVarStringArray | DevLong |
| GetLoggingLevel | DevVarStringArray | DevVarLongStringArray |
| GetLoggingTarget | DevString | DevVarStringArray |
| Init | DevVoid | DevVoid |
| Kill | DevVoid | DevVoid |
| LockDevice | DevVarLongStringArray | DevVoid |
| PolledDevice | DevVoid | DevVarStringArray |
| QueryClass | DevVoid | DevVarStringArray |
| QueryDevice | DevVoid | DevVarStringArray |
| QuerySubDevice | DevVoid | DevVarStringArray |
| QueryWizardClassProperty | DevString | DevVarStringArray |
| QueryWizardDevProperty | DevString | DevVarStringArray |
| ReLockDevices | DevVarStringArray | DevVoid |
| RemObjPolling | DevVarStringArray | DevVoid |
| RemoveLoggingTarget | DevVarStringArray | DevVoid |
| RestartServer | DevVoid | DevVoid |
| SetLoggingLevel | DevVarLongStringArray | DevVoid |
| StartLogging | DevVoid | DevVoid |
| StartPolling | DevVoid | DevVoid |
| State | DevVoid | DevState |
| Status | DevVoid | DevString |
| StopLogging | DevVoid | DevVoid |
| StopPolling | DevVoid | DevVoid |
| UnLockDevice | DevVarLongStringArray | DevLong |
| UpdObjPollingPeriod | DevVarLongStringArray | DevVoid |
| ZmqEventSubscriptionChange | DevVarStringArray | DevVarLongStringArray |

#### The Device Export sequence

The phase called Device Export is intended to prepare all the managed Device for operation and accessibility in the Tango Control System by the Device Server:

* The Device Server MUST take in charge of the Device Export

* The Device Export SHOULD bring the Device at the same State than after an Init Command.

* The Device Export SHOULD execute the Device Sign up of the Database([RFC-6]()), in order to be accessible locally and via network.


#### The Device Unexport sequence

The phase called Device Unexport is intended to prepare the all Device for stopping operate.

* The Device Server MUST take in charge of the Device Unexport

* The Device Unexport SHALL gracefully stop the Device operation, even if there are requests in progress.

* The Device Unexport SHALL gracefully stop the communication with any Tango Client 

* The Device Unexport SHOULD unregister from the Tango Control system by executing the Device Sign Out of the Database([RFC-6]()), in order to notify that all managed Device are not accessible
