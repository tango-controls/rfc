---
domain: rfc.tango-controls.org
shortname: 2/Device
name: Device Model
status: raw
editor: Vincent Hardion (vincent.hardion@maxiv.lu.se)
---

This document describes the Tango Device model specification version 5.0.

See also: X/Command, X/Attribute, X/Property, X/Pipe

## Preamble

Copyright (c) 2019 MAX IV Laboratory.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Device Specification

A Device is designed to represent any controlled object in Tango.
This specification is inspired by the comment written by A. Götz and E. Taurel in the tango.idl file from the original implementation (https://tango-controls.github.com)

### Related Specification

### Goals

The Tango Device aims to represents the fundamental interface for all TANGO objects. Directly inspired from the object oriented programming a Device object has data and actions.


Additionally, it aims to:

* Provide ...

* Be usable as ...

* Be compatible ...

### Use Cases

There are X main use cases for Template:

* To reduce the entry cost of RFC submission.

* To make coherent the RFC

...

## Specification

A Tango Device is a strict definition of a distributed object. 
* configuration are represented in the form of properties.
* data are represented in the form of attributes and pipes.
* actions are represented in the form of commands.

Its model can be represented as a defined tree which each elements are from a defined types: Class, Device, Property, Attribute, Command following the rules below:


* The Device is a distributed object which SHALL be accessed locally or via network.
* The Device SHALL be an instance of one Device Class (see RFC-9)
* The Device MAY have one or several Property, called Device Property
* The Device MAY have one or several Attribute, 
* The Device SHALL have one State attribute 
* The Device SHALL have one Status attribute
* The Device MAY have one or seeveral Pipe
* The Device MAY have one or several Command
* The Device SHALL have one unique identifier which represents its Name

* The Class MAY have one or more Device instance
* The Class MAY have one or several Property, called Class Property

### Devcie Interface

The Devcie Interface is a list of Attributes, Pipes and Commands provided by a Device Instance.
The Device instance SHALL expose (provides access) all Attributes, Pipes and Commands defined by its Device Class.

The Device instance MAY expose Attributes and/or Commands not defined by its Device Class. 
These are called Dynamic Attributes and/or Dynamic Commands respectively. 
The Dynamic Attributes and Dynamic Commands MAY be added to the interface during Device Initalisation phase and/or Device Operation. 

### Naming convention

* The Device's Name SHALL use the following convention:
``` ABNF
device-name = domain "/" family "/" member
domain = 1*VCHAR
family = 1*VCHAR
member = 1*VCHAR
``

### Device lifecycle

The Device lifecycle is:
* Devcie Initialisation phase
* Device Operation phase
* Device Destruction phase 

[TO-DO]
#### Device Initialization

* Object creation
* Devcie exporting

#### Device Operation

* Attribute read and write
* Command Call
* Device State handling

#### Device Destruction

