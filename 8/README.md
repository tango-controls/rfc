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

 The specication of '''Device Server''' aims the process of Device management, being it self a Device.

### Goals

 A Device Server in a Tango Controls System aims to control the process of communication and lifecycle of one or several Device.

Additionally, it aims to:

* Provide a blackbox that register the operation executed on every Device

* Be usable as a Device

### Use Cases

There are X main use cases for Device Server:

* to manage several Device in the same process

* to share the same channel of communication

* to serialise the operation per Device


## Specification

The Device Server manage the communication with one or several Device.  Additionally it enforces the behaviour compatibility with the Tango Control System and offer different meta service.

The Device Server SHALL follow these specification below in order to  control and monitor this communication:

* A Device Server represents/interfaces the process which manage one or several Device

* A Device Server is a also a Device itself.

* A Device MUST NOT be managed by more than one Device Server

The name of the Device Server SHALL follow this convention:
```ABNF
device-server-name = dserver "/" server "/" instance
dserver = "dserver" ; dserver is a reserved domain following the device-name convention.
server = family
instance = member
```

The specification introduces as well the definition of the process which are used for the Device Server Name:

* A Server is a type of process managed by a Device Server

* An Instance is an unique identifier as several processes of the same Server MAY exist in a Tango Control System

* A Server Instance is finally what identify the actual process in the Tango Control Sytem

* A Server Instance CAN be interfaced by only one Device Server


Additionally:
* A Device Server MAY be call DServer or Admin Device but the terminology Device Server is RECOMMENDED

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

The phase called Device Export is intended to prepare the Device for its operation and accessibility in the Tango Control System by the Device Server:

* The Device Server MUST take in charge of the Device Export

* The Device Export SHOULD bring the Device at the same State than after an Init Command.
* The Device Export SHOULD execute the Device Registration of the Database([RFC-6]()), in order to be accessible locally and via network.


#### The Device Unexport sequence

...
