---
domain: rfc.tango-controls.org
shortname: 2/Device
name: Device Model
status: raw
editor: David Erb (david.erb@maxiv.lu.se)
contributor: Piotr Goryl (piotr.goryl@s2innovation.com)
---

This document describes the Tango Device model specification version 5.0.

See also: 1/TANGO, 3/Command, 4/Attribute, 5/Property, 7/Pipe, 8/Server, 9/Class

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

### Related Specification

### Goals

The Tango Device represents the fundamental interface for all TANGO objects. Directly inspired from the object 
oriented programming a Device object has data and actions. It allows users of the Tango Controls System to access 
(read and/or write) information (data) stored in or processed by hardware or virtual devices and call actions which
may influence hardware state and behaviour.  Typically, access to data is provided via Device's Attributes and Pipes
and actions are initiated by calling Device's Commands.  

Additionally, it aims to:

* Provide a standard way of translating different communication protocols to a communication protocol implemented by
  Tango Controls. For example, the same generic GUI application may be used to control a power supply interfaced with
  the SCPI protocol and to control a stepper motor driver interfaced with the CAN bus.

* Be usable as a logical abstraction of hardware. For example the same GUI application may be used to control power 
  supplies powering magnets and powering heat coils.

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

* Remote monitoring of a complex device like aa electro-magnet, which is powered by power supplies and requires a cooling
  system may be provided by a Device with the following attributes: MagneticField, CoilTemperature, State and Status and 
  with the commands On() and Off(). This Device, to serve its interface is using information coming (directly or through
  other devices) from power supplies and cooling systems.  

## Specification

A Tango Device is a strict definition of a distributed object. 
* configuration is represented in the form of properties.
* data are represented in the form of Attributes and Pipes.
* actions are represented in the form of Commands.

Its model can be represented as a defined tree which each elements are from a defined types: Class, Device, Property, 
Attribute, Pipe, Command following the rules below:

* The Device is a distributed object which SHALL be accessible locally or via network.
* The Device SHALL be an instance of one Device Class, see [RFC-9]()
* The Device MAY have one or several Property, called Device Property, see [RFC-5]()
* The Device MAY have one or several Attribute, see [RFC-4]() 
* The Device MAY have one or several Pipe
* The Device MAY have one or several Command, see [RFC-3]()

* The Device SHALL have one Init command

* The Device SHALL have one State attribute 
* The Device SHALL have one Status attribute
* The Device SHALL have one State command
* The Device SHALL have one Status command


**Note**: Future specification may remove 'SHALL have' requirements for State and Status commands.

* The Device SHALL have one unique identifier which represents its Device Name

### Naming convention

* The Device Name SHALL use the following convention:

``` ABNF
device-name = domain "/" family "/" member
domain = 1*VCHAR
family = 1*VCHAR
member = 1*VCHAR
```

### Device Class

A Device Class is an association of a list of Device Class Properties, a list of Attributes, a list of Pipes, 
a list of Commands, a list of Device Properties with a Device Class Name.  

* The Device Class Name SHOULD reflect its instances application context. 
* The Device Class Name SHALL use the following convention:

``` ABNF
device-class-name = 1*VCHAR
```

* The Device Class MAY have one or more Device instance
* The Device Class MAY have one or several Property, called Class Property

* The Device SHALL expose (provide access to) all Attributes, Pipes and Commands defined by its Device Class.
 
### Device Interface

The Device Interface is a list of Attributes, Pipes and Commands provided by a Device.

* The Device MAY expose Attributes and/or Commands not defined by its Device Class. These are called 
  Dynamic Attributes and/or Dynamic Commands respectively, see [RFC-XX]().

* The Dynamic Attributes and Dynamic Commands MAY be added to the interface during Device Initialisation phase and/or 
  Device Operation phase. 

### Device lifecycle

The Device lifecycle is:
* Device MUST be in one of two phases: Exported or Not Exported.
* In the Exported phase, Device Interface MUST be accessible to actors of the Tango Controls System.
* In the Not Exported phase, Device Interface MUST NOT be accessible to actors of the Tango Controls System.
* At the transition from the phase Not Exported to the phase Exported the Device MUST be initialised as if its 
  Init Command was executed.
* At the transition from the phase Exported to the phase Not Exported the Device SHOULD gracefully be uninitialised. 

The device lifecycle MUST be implemented by a Device Server.

### Device State

Value of Device's State attribute MAY impact how the Device object respond to:

* Writing to a Device's Attribute or to a Device's Pipe  
* Calling a Device's Command

Device MAY not execute (block) the attribute write or the pipe write operation or the command execution code for certain States.
In such a case the the Device object SHALL respond with throwing an exception.  [TO-DO: specify which exception]
The way how a Device object respond to the above calls define a Device State Machine.

**NOTE**: Current implementation allows a Device object to block the operations irrespectively to the Device's State
attribute value. 

### Device Black Box


### Events


### Logging 


### Cache and polling mechanism
