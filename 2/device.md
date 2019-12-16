---
domain: rfc.tango-controls.org
shortname: 2/Device
name: Device Model
status: raw
editor: [Vincent Hardion](mailto:vincent.hardion@maxiv.lu.se)
contributors: [Piotr Goryl](mailto:piotr.goryl@s2innovation.com)
---

This document describes the Tango Device model specification version 5.0.

See also: [1/TANGO](/1), [3/Command](/3), [4/Attribute](/4), [5/Property](/5), [6/Database](/6), [7/Pipe](/7), 
          [8/Server](/8)

## Preamble

Copyright (c) 2019 MAX IV Laboratory.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public
License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later 
version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even 
the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more
details. You should have received a copy of the GNU General Public License along with this program; 
if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by 
the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", 
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Device Specification

A Device is designed to represent any controlled object in Tango Controls System.
This specification is inspired by the comment written by A. Götz and E. Taurel in the tango.idl file from 
the original implementation (https://github.com/tango-controls/tango-idl)

### Goals

The Tango Device represents the fundamental interface for all TANGO objects. Directly inspired by object-oriented programming, a Device object has data and actions. It allows users of the Tango Controls System to access 
(read and/or write) information (data) stored in or processed by hardware or virtual devices and call actions which
may influence hardware state and behaviour.  Typically, access to data is provided via Device's Attributes and Pipes
and actions are initiated by calling Device's Commands.  

Additionally, it aims to:

* Provide a standard way of translating different communication protocols to a communication protocol implemented by
  Tango Controls. For example, the same generic GUI application may be used to control a power supply interfaced with
  the SCPI protocol and to control a stepper motor driver interfaced with the CAN bus.

* Be usable as a logical abstraction of the hardware. For example, the same GUI application may be used to control power supplies which are powering magnets and powering heat coils.

* Expose, to Users and other Actors of the Tango Controls System, a semantic interface which is well understood, 
  intuitive, and match the object-oriented paradigm. For example, a power supply Device may be implemented in Java, 
  Python or C++ language. Irrespectively to the implementation language, Attributes *current* and *voltage* can be 
  implemented as field members of an object, while *on* and *off* commands can be implemented as methods of the 
  same object.     
  

### Use Cases

Typical use cases for Device are:

* It is common for particles accelerator systems to use a set of power supplies for powering a magnet system. 
  Power supplies may come from different vendors. These power supplies may use different communication interfaces
  and protocols for a remote control. However, there is a need to steer these power supplies from a remote locations in 
  a unified and consistent way. Steering includes setting and reading of voltages and currents as well as switching the 
  power supplies on and off, each individually. Having them integrated into a system as Devices, each with an unique name, 
  with Current and Voltage attributes and On() and Off() commands fulfills the requirement and assure consistency.

* Remote monitoring of a complex device like an electro-magnet, which is powered by power supplies and requires a cooling
  system may be provided by a Device with the following attributes: MagneticField, CoilTemperature, State and Status and 
  with the commands On() and Off(). This Device, to serve its interface is using information coming (directly or through
  other devices) from power supplies and cooling systems.  

## Specification

A Tango Device is a strict definition of a distributed object. 
* configuration is represented in the form of Properties.
* data are represented in the form of Attributes and Pipes.
* actions are represented in the form of Commands.

Its model can be represented as a defined tree which each element are from a defined type: Class, Device, Property, 
Attribute, Pipe, Command following the rules below:

* The Device is a distributed object which SHALL be accessible locally or via the network.
* The Device SHALL be an instance of one Class, see [Device Class section](#device-class).
* The Device MAY have one or several Property, called Device Property, see [RFC 5/Property](/5).
* The Device MAY have one or several Attribute, see [RFC 4/Attribute](/4).
* The Device MAY have one or several Pipe, see [RFC 7/Pipe](/7).
* The Device MAY have one or several Command, see [RFC 3/Command](/3).

* The Device SHALL have one Init Command.

* The Device SHALL have one State Attribute. 
* The Device SHALL have one Status Attribute.
* The Device SHALL have one State Command.
* The Device SHALL have one Status Command.


**Note**: Future specification may remove 'SHALL have' requirements for State and Status Commands.

* The Device SHALL have one unique identifier which represents its Device Name.

### Naming convention

* The Device Name SHALL use the following convention:

``` ABNF
device-name = domain "/" family "/" member
domain = 1*VCHAR
family = 1*VCHAR
member = 1*VCHAR
```

### Device Class

A Class is an association of a list of Class Properties, a list of Attributes, a list of Pipes, 
a list of Commands, a list of Device Properties with a Device Class Name.  

* The Class Name SHOULD reflect its instances application context. 
* The Class Name SHALL use the following convention:

``` ABNF
class-name = 1*VCHAR
```

* The Class MAY have one or more Device instance.
* The Class MAY have one or several Property, called Class Property.

* The Device SHALL have at least all Attributes, Pipes and Commands defined by its Class.
 
* The Device MAY have Attributes and/or Commands not defined by its Class. These are called 
  Dynamic Attributes and/or Dynamic Commands respectively, see [RFC-XX]().

* Dynamic Attributes and/or Dynamic Commands MAY be added to the Device during transition from the phase 
  Not Exported to the phase Exported.

* In the Exported phase, Dynamic Attributes and/or Dynamic Commands MAY be added to the Device.  

* In the Exported phase, Dynamic Attributes and/or Dynamic Commands MAY be removed from the Device.

### Device lifecycle

The Device lifecycle is:
* Device MUST be in one of two phases: Exported or Not Exported.
* In the Exported phase, Device MUST be accessible to actors of the Tango Controls System.
* In the Not Exported phase, Device MUST NOT be accessible to actors of the Tango Controls System.
* At the transition from the phase Not Exported to the phase Exported the Device MUST be initialised as if its 
  Init Command was executed.
* At the transition from the phase Exported to the phase Not Exported the Device SHOULD gracefully be uninitialised. 

* The Device Server MUST manage Device lifecycle.

### Init Command

The Init Command purpose is to re-initialise the Device.

* The Init Command, when called, SHALL in a sequence:
  * gracefully un-initialise the Device returning to a state before its first initialisation,
  * initialise the Device using the values of its Properties.

### Device State and Status

The State Attribute represents Device State. 

* The *data type* of State Attribute MUST be `DevState`. The *read value* of Status Attribute MUST be 
  of `DevState` data type.
* The State Attribute's *writable* metadata MUST be `READ`.
* The *data format* of State Attribute MUST be `SCALAR`.
* The *read value* of State Attribute SHOULD reflect the context of a running Device. If Device is related to hardware
  or a virtual entity or some process, the value of State Attribute SHOULD reflect the state of the entity or process.

* The *data type* of Status Attribute MUST `DevString`. The *read value* of Status Attribute MUST be 
  of `DevString` data type.
* The Status Attribute's `writable` metadata MUST be `READ`. 
* The `data format` of the Status Attribute MUST be `SCALAR`.

* The Status Attribute SHOULD provide status information related to the Device. 
* By default Status Attribute SHOULD indicate the Device State. 

* The State Command MUST return the same value as would be read from the State Attribute at the time 
  of the command execution.
* The Status Command MUST return the same value as would be read from the Status Attribute at the time of the command execution.

### State Machine
* By default State Attribute value SHALL be `UNKNOWN`. 
 
* By default the Device State SHOULD be in ALARM if at a given time the Device State is ON and at least one of Device 
  Attributes has the quality set to ALARM. 

Value of Device's State Attribute MAY impact how the Device responds to:

* Reading and writing of its Attribute or Pipe,  
* Calling a Command.

The Device MAY not execute (block) the Attribute or Pipe read or write operation or the command execution code 
for individual States. In such a case the Device SHALL respond with throwing a `DevFailed` exception.
The way how a Device responds to the above calls define a State Machine.

* A Device MAY use a State Machine.

### Reserved Device Names

However it is possible to use any Device Name as it is stated in [Naming convention section](#naming-convention),
some groups of names are reserved. One is Admin Device Name (see [RFC 8/Server](/8)), defined as follows: 

``` ABNF
admin-device-name = domain "/" family "/" member
domain = %i"dserver"
family = 1*VCHAR
member = 1*VCHAR
``` 

`admin-device-name` MUST not be used for Devices of other than `DServer` Device Class.  

### Reserved Class Names

Devices of some of Classes are used to provide standard Tango Controls System services. 
These Classes' Names SHOULD not be used for other purposes. Below is a list of reserved Class Names:

* `%i"DataBase"`, see [RFC 6/Database](/6)
* `%i"TangoAccessControl"`
* `%i"DServer"`
