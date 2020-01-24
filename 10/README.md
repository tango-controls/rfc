---
domain: rfc.tango-controls.org
shortname: 10/RequestReply
name: The Request-Reply protocol
status: raw
editor: 
---

This document describes The Request-Reply protocol. The Request-Reply protocol provides a two way in which the Tango 
Controls clients and servers communicate with each other.

See also: Y/OtherTemplate

## Preamble

Copyright (c) 2019 Tango Community.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public 
License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later 
version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY;
without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public 
License for more details. You should have received a copy of the GNU General Public License along with this program; 
if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by 
the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", 
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## The Request-Reply protocol Specification

The Request-Reply protocol defines:
* types and content of messages sent between Tango Controls objects,
* communication sequences,
* rules for establishing and tearing-down network connections.

### Goals

The Request-Reply protocol aims to standardise way of exchanging data between Tango Clients and Device Servers in 
a pattern where a client sends a request and the server reply with an answer. It SHALL allow for reading and writing 
Attributes and Pipes as well as for executing Commands.

Additionally, it aims to:

* Be independent of network transport/binary protocol,

* Define a rules for transparent reconnection,

* Allow for initiation and management of other communication protocols (like Publishe-Subsciber protocol) .

### Use Cases

There are few main use cases for The Request-Reply protocol:

* Calling Commands on Devies,
* Reading Attributes' and a Pipes' values and meta-data,
* Writing Attributes' and Pipe's data,
* Setting-up Publisher-Subscriber communication,

### Connection management


###
