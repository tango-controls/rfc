---
domain: rfc.tango-controls.org
shortname: 8/Device Server model
status: raw
editor: [Lorenzo Pivetta](mailto:lorenzo.pivetta@elettra.eu)
contributors: [Vincent Hardion]
---

This document describes the Tango Device Server model.

## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Related RFC specifications (TBD)
- 2/The Device object Model

## Tango Device Server model specification

This spefication formally document the Tango Device server model. Runtime implementation of the Device Server model in conforming Tango implementations MUST follow this specification.

### Goals

The Tango Device Server, or Device Server instance, is the operating system process where Tango Devices run. The Device Server main task is to offer one, or more, services to the clients. In a Tango control system, a Device Server instance has a unique identifier by which it can be referenced. A Device Server is able to host several Devices, instantiating the same or different Tango Classes.

## Specification

* The Device Server name is built as _DeviceServerName_/_InstanceName_, where _DeviceServerName_ is the name of the executable
* The Device Server instance reference SHALL be unique in a Tango Control system
* The Device Server SHALL host at least one Device
* The Device Server MAY host several Devices
* Hosted Devices MAY belong to different Classes
* The Device Server SHALL have a management device