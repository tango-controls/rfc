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
- logging levels: OFF, FATAL, ERROR, WARNING, INFO, DEBUG.
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

## Specification

### Logging Properties 
TLS SHALL use four Device Properties to control Device Server logging at startup.  The Properties may be set during runtime in the normal way.

#### logging_level property
SHALL controls the initial logging level of a device. It SHALL have the set of possible values: OFF, FATAL, ERROR, WARN, INFO or DEBUG. This property SHALL be overwritten by the verbose command line option (-v).

#### logging_target property
SHALL give the name of one or more Logging Target (see below), separated by commas.

#### logging_rft property
SHALL cause the maximum file size written to be the given value.  If a file grows past this size, SHALL be closed, renamed and a new file opened.

#### logging_path property
SHALL override the TANGO_LOG_PATH environment variable and SHALL only apply to classes derived from the DServer class.
The value SHALL specify where log files are written for Logging Target "file" (with no filename).

### Logging Targets
TLS SHALL allow Logging Targets, that is, destinations, for messages.  A Device Server SHALL emit messages to all targets (comma-separated) to its logging_target Property.
#### console
output SHALL be delivered to stdout
#### file
output SHALL be written to a standard location in a directory specified by TANGO_LOG_PATH if nonblank, otherwise in a directory created under /tmp/tango-*.
#### file::filename
output SHALL be written to the filename given.  File format SHALL be log4j compatible.
#### device::devicename
output SHALL be sent to the Tango Device given by the device name given.  The 
The Device running as devicename SHALL implement the Log Consumer interface (see below).

### Logging Levels
TLS SHALL support Logging Levels.  The Logging Level is meant to act as a filter for the amount of inform.  These are the logging levels, with typical meanings for when they are to be used.
- OFF: Nothing is logged
- FATAL: A fatal error occurred. The process is about to abort
- ERROR: An (unrecoverable) error occurred but the process is still alive
- WARN: An error occurred but could be recovered locally
- INFO: Provides information on important actions performed
- DEBUG: Generates detailed information describing the internal behavior of a device

Levels SHALL be ordered the following way:  
```DEBUG < INFO < WARN < ERROR < FATAL < OFF```  
For a given device, a level SHALL be said to be enabled if it is greater or equal to the logging level assigned to this device.
In other words, any logging request which level is lower than the device's logging level is ignored.

The all the Device's Logging Targets SHALL share the same device logging level.

### C++ API
TLS SHALL provide the following C++ macros for generating messages.

There SHALL be two forms, printf-like and stream-like.

printf-like: ```LOG_DEBUG(("Msg#%d - Hello world", i++));```

stream-like: ```DEBUG_STREAM << "Msg#" << i++ << "- Hello world" << endl;```

These two logging requests are equivalent. Note the double parenthesis in the printf version.

The examples just given are for the DEBUG log level.  There SHALL BE equivalent macros for the other log levels, except OFF.

TLS SHALL provide a means to log in the name of a device.  This SHALL be provided when the Device must is a member of a class inheriting from the ```Tango::LogAdapter``` class.

### Python API
TLS SHALL provide the folliwng Python forms of writing log messages:

basic logging: ```self.debug_stream("read_voltage(%s, %d)", self.host, self.port)```

logging with print: ```print >>self.debug_info, "read voltage attribute"```

logging with decorators: ```@PyTango.DebugIt()```

The examples just given are for the DEBUG log level.  There SHALL BE equivalent macros for the other log levels, except OFF.


### Java API
TLS SHALL provide configuration to allow Java to log messages in the following manner:   
```get_logger().debug(Initializing device  + get_name());```   
The example just given is for the DEBUG log level.  There SHALL BE equivalent macros for the other log levels, except OFF.


### Log Consumer
When a Device Target is specified as "device", this SHALL designate a Device Server which is implementing the Log Consumer interface.
To be a Log Consumer, a Tango Device SHALL implement just one Tango Command:   
```void log (Tango::DevVarStringArray details)```.   

The array of strings composing the argument SHALL be interpreted with the following meanings:
- details[0] : the timestamp in millisecond since epoch (01.01.1970)
- details[1] : the log level
- details[2] : the log source (i.e. device name)
- details[3] : the log message
- details[4] : the log NDC (contextual info) - Not used but reserved
- details[5] : the thread identifier (i.e. the thread from which the log request comes from)


### GUI for viewing logs
TLS SHALL include a GUI application called LogViewer or displaying logs pushed by the devices to the Tango logging system. 
LogViewer SHALL have the ability to fetch logs from different Tango devices and filter them by several properties.
The LogViewer SHALL have the following abilities:
- show logging messages from multiple devices simultaneously
- filter logging messages by Logging Level
- be able to read log files from disk

LogViewer SHALL have two modes: static and dynamic.   

- static: LogViewer SHALL be started with the name of the Log Consumer Device name and it SHALL receive all messages from Devices who have this name as their target.

- dynamic: LogViewer SHALL let the user use the GUI to specify which devices LogViewer will monitor.


## References
The TANGO Control System: Reference Section A.1.5: The device logging
http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node11.html#SECTION001115000000000000000

The TANGO Control System: Reference Section 8.3 The Tango Logging Service
http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node9.html#SECTION00930000000000000000

A.8 Tango log consumer
http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node11.html#sec:Tango-log-consumer

LogViewer Extension
https://tango-controls.readthedocs.io/en/latest/tools-and-extensions/built-in/logviewer/logviewer.html
