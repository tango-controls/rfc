---
domain: rfc.tango-controls.org
shortname: 1/TANGO
name: Tango Control System
status: raw
editor: Lorenzo Pivetta (lorenzo.pivetta@elettra.eu)
---

This document describes Tango, a control system framework. This RFC define the specification of a control system implementing the philosophy of Tango.

See also: Y/OtherTemplate

## Preamble

Copyright (c) 2019 European Synchrotron (ESRF).

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Specification
The TANGO control system is a toolkit for building distributed control systems which are device oriented. 

### Goals

Tango aims to be extremely flexible and can be used to interface any kind of hardware or software system which can be accessed via one of the software languages supported by Tango (C++, Python, Java and JavaScript).

The goal of Tango is to provide a way of representing all physical and software systems which need to be controlled by a control system as Tango Devices.

Devices are software components which have Commands (methods), Attributes (data) and Properties (configuration) and are accessible on the network or locally.

A Tango control system consists of a collection of Tango Devices, an (optional) Database and a set of applications for configuring and controlling the Devices via well defined Application Programmers Interfaces (APIs). Tango defines and maintains official APIs for C++, Python, Java, JavaScript and REST.

The Tango Database serves the purpose of defining and declaring all Devices in the control system and their configuration parameters. The Database also stores the network address of each Device and serves as Naming Service for clients who need to establish a connection to any or all Devices.

The software used in large facilities can be compared with the software for stock markets – huge amount of data must be displayed on the monitor in a real time and being processed and being saved in databases for post processing.

The Tango toolkit was mainly developed for research facilities needs, but the idea and concept (philosophy) behind was to create a framework for control systems in general. The concept is based on the idea that the concept of an object or component is ideally suited to designing and implementing control systems. TANGO was based on this concept and can be described as an object oriented control system. A number of concepts were defined as being necessary to implement a TANGO control system (ref "TANGO - AN OBJECT ORIENTED CONTROL SYSTEM BASED ON CORBA" by Chaize et. al., ICALEPCS 1999). These are:

1. Device network object with the minimum of functionality necessary for a distributed control object
2. Use CORBA communication protocol while hiding details
3. Support following communication paradigms - synchronous, asynchronous and event driven
4. Support the implementation of new devices by sub-classing Device and by composing new types of Devices from existing types of Devices
5. Support modern programming patterns like multi-threading
6. A naming database which stores configuration information
7. A generic set of Data Types
8. Monitors
9. Device caching
10. Scripting
11. Multi-platform



Additionally, it aims to:

* Provide ...

* Be usable as ...

* Be compatible ...

### Use Cases

There are X main use cases for Template:

* To reduce the entry cost of RFC submission.

* To make coherent the RFC

...
