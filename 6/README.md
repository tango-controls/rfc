---
domain: rfc.tango-controls.org
shortname: 6/Database
name:Database
status: draft
editor: 
contributors: [David Erb](mailto:david.erb@maxiv.lu.se)
---

This document describes the Tango Database specification.

## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Related RFC specifications (TBD)
- X/Device Model 
- X/Attributes 
- X/Properties 

## Tango Database specification

This specification is intended to formally document the Tango Database. 
Runtime implementations of the Database in conforming Tango implementations MUST follow this specification.


### Goals of the Database

* provide means for device servers to be configured from central control
* serves as nameserver such that clients are able to find a device server's network location at runtime by name

### Use Cases of the Database

* stores configuration data (properties) used at startup of a device server 
* acts as nameserver by storing the dynamic network addresses
* acts as permanent store of dynamic settings which need to be memorised
* ensures the uniqueness of device name and of aliases
* keeps list of controlled servers for Starter service
* limited set functionality provide by file as alternative to network database


### General Specification
The database service shall be implemented as a Tango Device Server.

In the Tango Control System, its database service will be available on a pre-known network address.

Clients MUST access the database service via TANGO commands requested on the database device.

As a Tango Device Server, the database SHALL provide State and Status.

A limited set of the database functionality to support Tango Device Server configuration shall be avaliable as a read-only file.
This is an option for use in the absence of a network service.

The database SHALL enforce uniqueness of device names and aliases.

The device name, the command name, the attribute name, the property name, the device alias name and the device server name SHALL BE case insensitive.

The device name, its domain, member and family fields and its alias maximum sizes SHALL BE at least:
- device name: 255
- domain field: 85
- family field: 85
- member field: 85
- device alias name: 255

### Devices
The database SHALL provide a means of defining and querying devices.

The database SHALL implement the following device-related commands:
- DBAddDeviceDbDeleteDevice
- DbGetClassForDevice
- DbGetClassInheritanceForDevice
- DbGetDeviceClassList
- DbGetDeviceDomainList
- DbGetDeviceFamilyList
- DbGetDeviceInfo
- DbGetDeviceList
- DbGetDeviceWideList
- DbGetDeviceMemberList
- DbGetDeviceServerClassList

### Classes
The database SHALL provide a means of querying device server classes.

The database SHALL implement the following class-related commands:
- DbGetClassList
- DbGetDeviceClassList
- DbGetDeviceServerClassList


### Properties
The database SHALL provide a means of setting and getting properties for classes, devices, attributes, pipes and free objects.

The database SHALL provide a means to retrieve the history of property value settings.

The database SHALL implement the following property-related commands:

##### ClassAttributeProperty
- DbDeleteClassAttributeProperty
- DbGetClassAttributeProperty
- DbGetClassAttributeProperty2
- DbGetClassAttributePropertyHist
- DbPutClassAttributeProperty
- DbPutClassAttributeProperty2

##### ClassProperty
- DbDeleteClassProperty
- DbGetClassProperty
- DbGetClassPropertyHist
- DbGetClassPropertyList
- DbPutClassProperty

##### DeviceAttributeProperty
- DbDeleteDeviceAttributeProperty
- DbGetDeviceAttributeProperty
- DbGetDeviceAttributeProperty2
- DbGetDeviceAttributePropertyHist
- DbPutDeviceAttributeProperty
- DbPutDeviceAttributeProperty2
- DbDeleteAllDeviceAttributeProperty

##### DeviceProperty
- DbDeleteDeviceProperty
- DbGetDeviceProperty
- DbGetDevicePropertyHist
- DbGetDevicePropertyList
- DbPutDeviceProperty

##### Free Object Property
- DbDeleteProperty
- DbGetProperty
- DbGetPropertyHist
- DbGetPropertyList
- DbPutProperty

##### ClassPipeProperty
- DbGetClassPipeProperty
- DbDeleteClassPipeProperty
- DbPutClassPipeProperty
- DbGetClassPipePropertyHist

##### DevicePipeProperty
- DbGetDevicePipeProperty
- DbDeleteDevicePipeProperty
- DbDeleteAllDevicePipeProperty
- DbPutDevicePipeProperty
- DbGetDevicePipePropertyHist


### Attributes
The database SHALL provide a means of setting and getting attribute values for a class or device.

The database SHALL implement the following attribute-related commands:

##### ClassAttribute
- DbDeleteClassAttribute
- DbGetClassAttributeList

##### DeviceAttribute
- DbDeleteDeviceAttribute
- DbGetDeviceAttributeList


### Aliases
The database SHALL provide a means of setting and getting a mapping from alias to device name.

The database SHALL provide a means of setting and getting a mapping from alias to device attribute name.

The database SHALL implement the following attribute-related commands:

##### AttributeAlias
- DbDeleteAttributeAlias
- DbGetAttributeAlias
- DbGetAttributeAliasList
- DbPutAttributeAlias
- DbGetAttributeAlias2
- DbGetAliasAttribute

##### DeviceAlias
- DbDeleteDeviceAlias
- DbGetAliasDevice
- DbGetDeviceAlias
- DbGetDeviceAliasList
- DbPutDeviceAlias

### Hosts/Servers
The database SHALL provide a means of defining hosts and servers.

The database SHALL implement the following commands:
##### Host
- DbGetHostList
- DbGetHostServerList
- DbGetHostServersInfo

##### Server
- DBAddServer
- DbDeleteServer
- DbGetServerList
- DbGetServerNameList
- DbRenameServer

##### ServerInfo 
- DbDeleteServerInfo
- DbGetServerInfo
- DbPutServerInfo

### Pipes
The database SHALL provide a means of querying and deleting pipes.

The database SHALL implement the following pipe-related commands:

##### ClassPipe 
- DbDeleteClassPipe
- DbGetClassPipeList

##### DevicePipe
- DbDeleteDevicePipe
- DbGetDevicePipeList

### Free Objects

The database SHALL provide a means of querying Free Objects.

The database SHALL implement this free object-related command:
- DbGetObjectList

### Exported Devices

The database SHALL provide a means to register the network address of a running Device Server and events.

The database SHALL implement the following free registry-related commands:
- DbExportDevice
- DbUnExportDevice
- DbGetDeviceExportedList
- DbGetExportedDeviceListForClass
- DbImportDevice
- DbExportEvent
- DbImportEvent
- DbUnExportEvent
- DbUnExportServer


### Miscellaneous
The database SHALL provide the following miscellaneous commands.
- DbGetInstanceNameList
- DbGetForwardedAttributeListForDevice
- DbInfo
- ResetTimingValues
- DbGetDataForServerCache
- DbMySqlSelect
- DbGetCSDbServerList

## Database as a file
This section is To Be Done.


## Related documentation
[Tango Controls Documentation 9.3.3](https://buildmedia.readthedocs.org/media/pdf/tango-controls/latest/tango-controls.pdf)

[PyTango Documentation Release 8.1.6](http://www.esrf.eu/computing/cs/tango/pytango/v818/PyTango.pdf)

[Certified Database Device Server Reference Implementation](https://www.tango-controls.org/developers/dsc/ds/421/)

[The TANGO device Server model](http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node4.html)

[Multiple database servers within a Tango control system](https://tango-controls.readthedocs.io/en/latest/administration/deployment/multiple-sql-dbs.html)

[Memorized attribute](https://tango-controls.readthedocs.io/en/latest/development/device-api/memorized-attribute.html)

[Introduction to Astor](https://tango-controls.readthedocs.io/en/latest/tools-and-extensions/built-in/astor/introduction.html)
