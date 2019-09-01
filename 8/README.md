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

 The specication of '''Device Server''' aims to define a standard Device representing a process of Device management.

### Goals

 A Device Server in a Tango Controls aims to control the process of communication and lifecycle of the Device.

Additionally, it aims to:

* Provide a blackbox that register the operation on a Device

* Be usable as a Device

### Use Cases

There are X main use cases for Device Server:

* to manage several Device in the same process

* to share the same channel of communication

* to serialise the operation per Device

## Specification
The Device Server can manage the communication with one or several Device.  Additionally it enforces the behaviour compatibility with the Tango Control System and offer different meta service.

The Device Server SHALL follow these specification below in order to  control and monitor this communication:

* The Device Server MUST be a Device it self.

Additionally,
* A Device Server MAY be call DServer or Admin Device but the term Device Server is RECOMMENDED

#### Device Server interface

* Attribute read and write
* Command Call
* Device State handling

#### The Device Export sequence

The phase called Device Export is intended to prepare the Device for its operation and accessibility in the Tango Control System by the Device Server:

* The Device Server MUST take in charge of the Device Export

* The Device Export SHOULD bring the Device at the same State than after an Init Command.
* The Device Export SHOULD execute the Device Registration of the Database([RFC-6]()), in order to be accessible locally and via network.


#### The Device Unexport sequence

...
