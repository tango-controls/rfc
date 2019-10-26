domain: rfc.tango-controls.org
shortname: XX/Logging
name: The Tango Logging Service
status: raw
editor: 
contributors:
  - David Erb `<at maxiv.lu.se - david.erb>`

## Introduction
This document describes the Tango Logging Service (TLS).
The TLS allows Tango Devices to print messages to be: 
- displayed on a console (the classical way)
- sent to a file
- sent to specific Tango device called log consumer
- or more than one of the above at the same time

See also: 2/Device, 4/Attribute

## Preamble
Copyright (c) 2019 Tango Controls
This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.
This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Goals
This specification will describe the Tango Logging Service (TLS). 

It will describe and specify such things as:
- how logging is statically configured for a Device, that is, configured from Device Properties for start up.
- some logging terms: level, target, rft and path.
- what are logging targets: console, file, device
- what is the logging environment variable TANGO_LOG_PATH
- logging levels: OFF,  FATAL, ERROR, WARNING, INFO, DEBUG.
- logging commands available to all Devices
- the standard Tango Log Consumer Device and its interface (static mode, dynamic mode).

## Use Cases
The Tango Logging Service (TLS) provides the programmer with a convenient way to print information which helps to:
- debug the software
- report on errors
- give regular information to user
- provide timely information of the activities of the Device
- report the version of the softare
- provide clues to the internal changes happening in the Device

At run-time, the TLS supports such activities as:
- changing logging verbosity and destination of any Tango Device
- capture logs to files
- display log entries on the command line console
- interactively view log entries as they are produced by a remote Tango Device
- review logs already written to files
- make graphical dashboards with such tools as Kibana and Grafana


## References
The TANGO Control System: Reference Section A.1.5: The device logging
http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node11.html#SECTION001115000000000000000

The TANGO Control System: Reference Section 8.3 The Tango Logging Service
http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node9.html#SECTION00930000000000000000

Monitoring and Alarms in MAX IV
https://indico.esrf.fr/indico/event/10/contribution/14/material/slides/1.pdf

LogViewer Extension
https://tango-controls.readthedocs.io/en/latest/tools-and-extensions/built-in/logviewer/logviewer.html
