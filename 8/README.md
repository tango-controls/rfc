---
domain: rfc.tango-controls.org
shortname: X/SERVER
name: Device Server Model
status: raw
editor: 
---

This document provides Device Server specification.

See also: 2/Device

## Preamble

Copyright (c) 2019 Tango Community.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Device Server specification
Template is a ...

### Goals

 aims to ...

Additionally, it aims to:

* Provide ...

* Be usable as ...

* Be compatible ...

### Use Cases

There are X main use cases for Device Server:

* To reduce the entry cost of RFC submission.

* To make coherent the RFC


#### Device Initialization

Device Initialisation phase is intended to prepare Device Object for Operation. 

Device Initialization SHOULD be divided into two subsequent steps:

1) Object creation, when an object is created in a space of a Device Server process. After this phase Device object 
   SHALL be accessible locally and via network. During this phase the same `user code` which is executed when the 
   Device Init command is executed MAY be called.

2) Device exporting, when Device is made visible within the Tango Controls system. It SHALL use a Database System 
   ([RFC-6]()) to provide information on how other actors in the system can access its interface. 


The Device Server MUST implement at least Object creation step.

#### Device Operation

* Attribute read and write
* Command Call
* Device State handling

#### Device Destruction

...
