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
* provide a means of defining and querying devices.
* provide means for device servers to be configured from central control
* serve as nameserver such that the Devices are accessible by name

### Features of the Database

* store configuration data (properties) used at startup of a device server 
* act as nameserver by storing the dynamic network addresses
* act as permanent store of dynamic settings which need to be memorized
* ensure the uniqueness of device name and of aliases
* keep list of controlled servers which are to be started and run on a particular host
* limited set functionality provide by file as alternative to network database

### Use Cases of the Database

* register a new device server
* stores the last user setting point of the current provided by a power supply
* define persistent configuration properties to be used by a camera every time it starts up
* allow a subscriber or client to get connect to a stepper motor
* define properties for all instances of a class of vaccuum pump
* provide the network ID of a device such as an electrometer, so a user can check if it is online or not 
* a user would like to rollback the configuration of a Device when its initialization is failing
* remove "empty" Devices from Database to clean it up
* get all Devices of a given class of temperature gauges
* user wants to see the history (last ten values) of a Property
* a monitor tool wants the Info of a not running detector Device (exported, host, server)
* a boot-up process wants to get Starter Level configuration for a list of Devices

## Specification
The database SHALL provide a means of defining and querying devices.

As the Tango database service is a central service of the Tango control system, 
it SHALL be designed to be highly available and support a high level of client requests 
(e.g we have peaks of 3500 SQL requests/ s at SOLEIL on the accelerators control system).

The database service SHALL be implemented as a Tango Device Server.

The database service SHALL be implemented with:
- a frontend: as Tango Device Server
- a backend: a query and persistency engine (i.e. SQL or NoSQL engine), or a lighter ( i.e. reduced set of functionalities) engine with a single file per device server.

The Database Device SHALL have no Class Properties or Device Properties.

In the Tango Control System, its database service SHALL be available on a pre-known network address.

Clients and subscribers SHOULD access the database service via TANGO commands requested on the database device.

As a Tango Device Server, the database SHALL provide State and Status.

A limited set of the database functionality to support Tango Device Server configuration SHALL be available as a read-only file.
This is an option for use in the absence of a network service.

The database SHALL enforce uniqueness of device names and aliases.

The Database SHALL enforce the naming convention defined in all Tango RFCs
for device name, the command name, the attribute name, the property name, the device alias name and the device server name.

The device name, its domain, member and family fields and its alias maximum sizes SHALL BE at least:
- device name: 255
- domain field: 85
- family field: 85
- member field: 85
- device alias name: 255

## List of Commands Grouped by Functionality

### Devices

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
The database SHALL provide for the persistent storage of Attributes of classes and devices.

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
The database SHALL provide a means of managing information about pipes.

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

## List of Database commands and description
<table>
	<tr>
		<td> <center><b> Name </b></center> </td>
		<td> <center><b> Input type </b></center> </td>
		<td> <center><b> Output type </b></center> </td>
		<td> <center><b> Level </b></center> </td>
		<td> <center><b> Description </b></center> </td>
	</tr>
	<tr>
		<td> <a href="CmdState.html"> State </a> </td>
		<td> DEV_VOID </td>
		<td> DEV_STATE </td>
		<td> OPERATOR </td>
		<td> This command gets the device state (stored in its <i>device_state</i> data member) and returns it to the caller. </td>
	</tr>
	<tr>
		<td> <a href="CmdStatus.html"> Status </a> </td>
		<td> DEV_VOID </td>
		<td> CONST_DEV_STRING </td>
		<td> OPERATOR </td>
		<td> This command gets the device status (stored in its <i>device_status</i> data member) and returns it to the caller. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbAddDevice.html"> DbAddDevice </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Add a Tango class device to a specific device server </td>
	</tr>
	<tr>
		<td> <a href="CmdDbAddServer.html"> DbAddServer </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create a device server process entry in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteAttributeAlias.html"> DbDeleteAttributeAlias </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete an attribute alias. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteClassAttribute.html"> DbDeleteClassAttribute </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> delete a class attribute and all its properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteClassAttributeProperty.html"> DbDeleteClassAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> delete class attribute properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteClassProperty.html"> DbDeleteClassProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete class properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDevice.html"> DbDeleteDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete a device from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDeviceAlias.html"> DbDeleteDeviceAlias </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete a device alias. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDeviceAttribute.html"> DbDeleteDeviceAttribute </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete  device attribute properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDeviceAttributeProperty.html"> DbDeleteDeviceAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> delete a device attribute property from the database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDeviceProperty.html"> DbDeleteDeviceProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete device property(ies) </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteProperty.html"> DbDeleteProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete free property from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteServer.html"> DbDeleteServer </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete server from the database but dont delete device properties </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteServerInfo.html"> DbDeleteServerInfo </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> delete info related to a Tango devvice server process </td>
	</tr>
	<tr>
		<td> <a href="CmdDbExportDevice.html"> DbExportDevice </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Export a device to the database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbExportEvent.html"> DbExportEvent </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Export Event channel to database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetAliasDevice.html"> DbGetAliasDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_STRING </td>
		<td> OPERATOR </td>
		<td> Get device name from its alias. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetAttributeAlias.html"> DbGetAttributeAlias </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_STRING </td>
		<td> OPERATOR </td>
		<td> Get the attribute name for the given alias.<Br>
			If alias not found in database, returns an empty string. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetAttributeAliasList.html"> DbGetAttributeAliasList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get attribute alias list for a specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassAttributeList.html"> DbGetClassAttributeList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get attrilute list for a given Tango class with a specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassAttributeProperty.html"> DbGetClassAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get Tango class property(ies) value </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassAttributeProperty2.html"> DbGetClassAttributeProperty2 </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> This command supports array property compared to the old command called<Br>
			DbGetClassAttributeProperty. The old command has not been deleted from the<Br>
			server for compatibility reasons. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassAttributePropertyHist.html"> DbGetClassAttributePropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve Tango class attribute property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassForDevice.html"> DbGetClassForDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_STRING </td>
		<td> OPERATOR </td>
		<td> Get Tango class for the specified device. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassInheritanceForDevice.html"> DbGetClassInheritanceForDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get class inheritance for the specified device. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassList.html"> DbGetClassList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get Tango class list with a specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassProperty.html"> DbGetClassProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> None. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassPropertyHist.html"> DbGetClassPropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve Tango class property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassPropertyList.html"> DbGetClassPropertyList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get property list for a given Tango class with a specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceAlias.html"> DbGetDeviceAlias </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_STRING </td>
		<td> OPERATOR </td>
		<td> Return alias for device name if found. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceAliasList.html"> DbGetDeviceAliasList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get device alias name with a specific filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceAttributeList.html"> DbGetDeviceAttributeList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Return list of attributes matching the wildcard<Br>
			 for the specified device </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceAttributeProperty.html"> DbGetDeviceAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get device attribute property(ies) value </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceAttributeProperty2.html"> DbGetDeviceAttributeProperty2 </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve device attribute properties. This command has the possibility to retrieve<Br>
			device attribute properties which are arrays. It is not possible with the old<Br>
			DbGetDeviceAttributeProperty command. Nevertheless, the old command has not been<Br>
			deleted for compatibility reason </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceAttributePropertyHist.html"> DbGetDeviceAttributePropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve device attribute property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceClassList.html"> DbGetDeviceClassList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get Tango classes/device list embedded in a specific device server </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceDomainList.html"> DbGetDeviceDomainList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get list of device domain name matching the specified </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceExportedList.html"> DbGetDeviceExportedList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get a list of exported devices whose names satisfy the filter (wildcard is </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceFamilyList.html"> DbGetDeviceFamilyList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get a list of device name families for device name matching the<Br>
			specified wildcard </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceInfo.html"> DbGetDeviceInfo </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_LONGSTRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Returns info from DbImportDevice and started/stopped dates. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceList.html"> DbGetDeviceList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get a list of devices for specified server and class. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceWideList.html"> DbGetDeviceWideList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get a list of devices whose names satisfy the filter. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceMemberList.html"> DbGetDeviceMemberList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get a list of device name members for device name matching the<Br>
			specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceProperty.html"> DbGetDeviceProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> None. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDevicePropertyHist.html"> DbGetDevicePropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve device  property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDevicePropertyList.html"> DbGetDevicePropertyList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get property list belonging to the specified device and with<Br>
			name matching the specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDeviceServerClassList.html"> DbGetDeviceServerClassList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get list of Tango classes for a device server </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetExportdDeviceListForClass.html"> DbGetExportdDeviceListForClass </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Query the database for device exported for the specified class. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetHostList.html"> DbGetHostList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get host list with name matching the specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetHostServerList.html"> DbGetHostServerList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get list of device server process name running on host with name matching<Br>
			the specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetHostServersInfo.html"> DbGetHostServersInfo </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get info about all servers running on specified host, name, mode and level </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetInstanceNameList.html"> DbGetInstanceNameList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Returns the instance names found for specified server. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetObjectList.html"> DbGetObjectList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get list of free object defined in database with name<Br>
			matching the specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetProperty.html"> DbGetProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get free object property </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetPropertyHist.html"> DbGetPropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve object  property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetPropertyList.html"> DbGetPropertyList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get list of property defined for a free object and matching the<Br>
			specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetServerInfo.html"> DbGetServerInfo </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get info about host, mode and level for specified server </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetServerList.html"> DbGetServerList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get list of device server process defined in database<Br>
			with name matching the specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetServerNameList.html"> DbGetServerNameList </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Returns the list of server names found for the wildcard specified.<Br>
			It returns only the server executable name without instance name as DbGetServerList. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbImportDevice.html"> DbImportDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_LONGSTRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Import a device from the database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbImportEvent.html"> DbImportEvent </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_LONGSTRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get event channel info from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbInfo.html"> DbInfo </a> </td>
		<td> DEV_VOID </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get miscellaneous numbers on information<Br>
			stored in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutAttributeAlias.html"> DbPutAttributeAlias </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Define an alias for an attribute </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutClassAttributeProperty.html"> DbPutClassAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create/Update class attribute property(ies) in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutClassAttributeProperty2.html"> DbPutClassAttributeProperty2 </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> This command adds support for array properties compared to the previous one<Br>
			called DbPutClassAttributeProperty. The old comman is still there for compatibility reason </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutClassProperty.html"> DbPutClassProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create / Update class property(ies) </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutDeviceAlias.html"> DbPutDeviceAlias </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Define alias for  a given device name </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutDeviceAttributeProperty.html"> DbPutDeviceAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create/Update device attribute property(ies) in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutDeviceAttributeProperty2.html"> DbPutDeviceAttributeProperty2 </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Put device attribute property. This command adds the possibility to have attribute property<Br>
			which are arrays. Not possible with the old DbPutDeviceAttributeProperty command.<Br>
			This old command is not deleted for compatibility reasons. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutDeviceProperty.html"> DbPutDeviceProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create / Update device property(ies) </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutProperty.html"> DbPutProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create / Update free object property(ies) </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutServerInfo.html"> DbPutServerInfo </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Update server info including host, mode and level </td>
	</tr>
	<tr>
		<td> <a href="CmdDbUnExportDevice.html"> DbUnExportDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Mark a device as non exported in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbUnExportEvent.html"> DbUnExportEvent </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Mark one event channel as non exported in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbUnExportServer.html"> DbUnExportServer </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Mark all devices belonging to a specified device server<Br>
			process as non exported </td>
	</tr>
	<tr>
		<td> <a href="CmdResetTimingValues.html"> ResetTimingValues </a> </td>
		<td> DEV_VOID </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Reset the timing attribute values. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDataForServerCache.html"> DbGetDataForServerCache </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> This command returns all the data needed by a device server process during its<Br>
			startup sequence. The aim of this command is to minimize database access during<Br>
			device server startup sequence. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteAllDeviceAttributeProperty.html"> DbDeleteAllDeviceAttributeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete all attribute properties for the specified device attribute(s) </td>
	</tr>
	<tr>
		<td> <a href="CmdDbMySqlSelect.html"> DbMySqlSelect </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_LONGSTRINGARRAY </td>
		<td> OPERATOR </td>
		<td> This is a very low level command.<Br>
			It executes the specified  SELECT command on TANGO database and returns its result without filter. </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetCSDbServerList.html"> DbGetCSDbServerList </a> </td>
		<td> DEV_VOID </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get a list of host:port for all database server defined in the control system </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetAttributeAlias2.html"> DbGetAttributeAlias2 </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_STRING </td>
		<td> OPERATOR </td>
		<td> Get the attribute alias from the attribute name.<Br>
			Returns one empty string if nothing found in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetAliasAttribute.html"> DbGetAliasAttribute </a> </td>
		<td> DEV_STRING </td>
		<td> DEV_STRING </td>
		<td> OPERATOR </td>
		<td> Get the attribute name from the given alias.<Br>
			If the given alias is not found in database, returns an empty string </td>
	</tr>
	<tr>
		<td> <a href="CmdDbRenameServer.html"> DbRenameServer </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Rename a device server process </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassPipeProperty.html"> DbGetClassPipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve class pipe properties </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDevicePipeProperty.html"> DbGetDevicePipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve device pipe properties </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteClassPipe.html"> DbDeleteClassPipe </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete a class pipe and all its properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDevicePipe.html"> DbDeleteDevicePipe </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete device pipe properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteClassPipeProperty.html"> DbDeleteClassPipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete class pipe properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteDevicePipeProperty.html"> DbDeleteDevicePipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete device pipe properties from database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassPipeList.html"> DbGetClassPipeList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get pipe list for a given Tango class with a specified filter </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDevicePipeList.html"> DbGetDevicePipeList </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Return list of pipes matching the wildcard for the specified device </td>
	</tr>
	<tr>
		<td> <a href="CmdDbDeleteAllDevicePipeProperty.html"> DbDeleteAllDevicePipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Delete all pipe properties for the specified device pipe(s) </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutClassPipeProperty.html"> DbPutClassPipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create/Update class pipe property(ies) in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbPutDevicePipeProperty.html"> DbPutDevicePipeProperty </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEV_VOID </td>
		<td> OPERATOR </td>
		<td> Create/Update device pipe property(ies) in database </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetClassPipePropertyHist.html"> DbGetClassPipePropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve Tango class pipe property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetDevicePipePropertyHist.html"> DbGetDevicePipePropertyHist </a> </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Retrieve device pipe property history </td>
	</tr>
	<tr>
		<td> <a href="CmdDbGetForwardedAttributeListForDevice.html"> DbGetForwardedAttributeListForDevice </a> </td>
		<td> DEV_STRING </td>
		<td> DEVVAR_STRINGARRAY </td>
		<td> OPERATOR </td>
		<td> Get the list of devices using forwarded attribute(s) from specified device </td>
	</tr>
</table>

## Database as a file
For Device servers not able to access the Tango database (most of the time due to network route or security reason), 
it is possible to start them using a file instead of a real database. This is done via the Device server command line option:  
```-file=<file name>```  

In this case, the Database SHALL handle the following functionality using the specified file:  
* getting, setting and deleting class properties  
* getting, setting and deleting device properties  
* getting, setting and deleting class attribute properties  
* getting, setting and deleting device attribute properties  

The following set of limitations exist:  
* no check that the same Device server is running twice
* no Device or attribute alias name
* in case of several Device servers running on the same host, the user must manually manage a list of already used network ports

### File syntax
The file is an ASCII file.  It syntax is defined completely in .

Generally, comment starts with the ’#’ character and a blank line is ignored.

#### File syntax: Devices definition.
DEVICE is the keyword to declare a device(s) definition sequence. The general syntax SHALL be   
```<DS name>/<inst name>/DEVICE/<Class name>: dev1,dev2,dev3  ```
Device(s) name can follow on next line if the last line character is ’\’

#### File syntax: Device property definition. 
The general device property SHALL be  
```<device name>-><property name>: <property value>```  
In case of array, the array element delimiter is the character ’,’. Array definition can be splitted on several lines if the last line character is ’\’. Allowed characters after the ’:’ delimiter are space, tabulation or nothing.

A device string property with special characters (spaces). The ’’ character is used to delimit the string

#### File syntax: Device attribute property definition. 
The general device attribute property syntax SHALL be   
```<device name>/<attribute name>-><property name>: <property value>```  
Allowed characters after the ’:’ delimiter are space, tabulation or nothing.

#### File syntax: Class property definition. 
The general class property syntax SHALL be  
```CLASS/<class name>-><property name>: <property value>```  

CLASS is the keyword to declare a class property definition. Allowed characters after the ’:’ delimiter are space, tabulation or nothing. 
The ’’ characters around the property value are mandatory due to the ’/’ character contains in the property value.


## Related documentation
[Tango Controls Documentation](https://buildmedia.readthedocs.org/media/pdf/tango-controls/latest/tango-controls.pdf)

[PyTango Documentation Release](https://readthedocs.org/projects/pytango/downloads/pdf/stable/pytango.pdf)

[DataBase Tango Cpp Class](http://www.esrf.eu/computing/cs/tango/tango_doc/ds_doc/tango-ds/System/dbase/FullDocument.html)

[Certified Database Device Server Reference Implementation](https://www.tango-controls.org/developers/dsc/ds/421/)

[The TANGO device Server model](http://www.esrf.eu/computing/cs/tango/tango_doc/kernel_doc/ds_prog/node4.html)

[Multiple database servers within a Tango control system](https://tango-controls.readthedocs.io/en/latest/administration/deployment/multiple-sql-dbs.html)

[Memorized attribute](https://tango-controls.readthedocs.io/en/latest/development/device-api/memorized-attribute.html)

[Introduction to Astor](https://tango-controls.readthedocs.io/en/latest/tools-and-extensions/built-in/astor/introduction.html)

[Starter Tango Cpp Class](http://www.esrf.eu/computing/cs/tango/tango_doc/ds_doc/tango-ds/System/starter/)

[Using Database Object](https://tango-controls.readthedocs.io/en/9.2.5/tutorials-and-howtos/how-tos/how-to-pytango.html#using-database-object)

[Device server using file as database](https://tango-controls.readthedocs.io/en/latest/administration/deployment/without-sql-db.html)

[Database API](http://www.esrf.eu/computing/cs/tango/pytango/v920/database.html)

[The property file syntax](https://tango-controls.readthedocs.io/en/latest/administration/deployment/property-file.html)