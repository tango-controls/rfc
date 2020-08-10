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

## Specification

### Client duty
#### Reconnection
TODO Describe here how to check the connection and in which condition should the client reconnect

### Version compatibility
TODO describe here in which condition
TODO decide which version the specification should focus on i.e version 5

### Protocol
#### Message
SHOULD include:
* client process idendification i.e pid
* source

#### Exception

#### Timeout
#### Synchronous request
#### Asynchronous request
TODO understand the PUSH_BACK model. CORBA or more generic?

#### Cache

#### Serialisation

### Blackbox
A blackbox system should record every REQUEST on the Device especially:

Attribute case
* Read Attribute request,
  Should register the attribute name, the id of the client (often the pid), the version of TANGO and the Source (DevSource)
* Write or WriteRead Attribute request,
  Should register the attribute name, attribute value (only for writing?) (AttributeValueList_4), the id of the client (often the pid) and the version of TANGO
* reading an Attribute configuration # (Op_Get_Attr_Config);
* changing an Attribute Configuration # (Op_Set_Attr_Config);
* Read the Attribute History 

Pipe case
* Read Pipe request,
  Should register the pipe name, the id of the client (often the pid), the version of TANGO and the Source (DevSource)
* Write Pipe request,
  Should register the pipe name, attribute value, the id of the client (often the pid) and 0?, the version of TANGO_PIPE? 
* WriteRead Pipe request,
  Should register the pipe name, attribute value, the id of the client (often the pid) and 1?, the version of TANGO_PIPE? 
* reading an Pipe configuration # (Op_Get_Pipe_Config_5);
* changing an Pipe Configuration # (Op_Set_Pipe_Config_5);
* Read the Pipe History 

Command case
* listing the list of Command Name [//]: #  (Op_Command_list);
* execute a Command Name
Should register the command name, the id of the client (often the pid), the version of TANGO and the Source (DevSource)

* any operation like reading the request of Command History [//]: #   Op_Command_inout_history_4,

Operations case:
* reading the BlackBox itself #  (Op_BlackBox);
* reading the device (Attribute?) name # (Attr_Name)
* request the adm_name attribute # (Attr_AdmName);
* request thedescription attribute # (Attr_Description);
* read the information of a device command  # (Op_Command);
* read the information of a device # (Op_Info);
* executing Ping # (Op_Ping);


NOTE All GROUPED REQUESTS should be logged once with the same information as the unique request

### Device Locking
A Client has the possibility to avoid any modification of a Device from other Clients. This system is so-called Device Lock. The Device Server has here the role and the responsibility to apply this mechanism.

The following specifies the Device Lock system:  
* Only one Device Lock per Device SHALL be activated.
* A Device Lock SHALL be only owned by one Client.

When a Client activates a Device Lock, the Server MUST reject any of the following request from other Client:
* Command call except for State Command, Status Command and all commands listed in the Allowed Commands list (described further down).
* Write Attribute Request (including the atomic Write Read request)
* Write Pipe Request (including the atomic Write Read request)
* Setting Attribute Configuration
* Setting Pipe Configuration
* Setting Polling Configuration
* Setting Logging Configuration
In general it is RECOMMANDED to block any request resulting a mutation of a Device.

An Allowed Command is not affected by any the Device Lock, even this implies a modification of the Device state. The DeviceServer define a list of Allowed Command per Device Class which can only be changed at the Initialisation phase (TODO find the exact term) of the Device Server.
TODO: Explain how to set/get this list?

Other Clients have the possibility to check a Device Lock is currently activated (TODO See DeviceServer command)

The request of a Device Lock is done throught the DeviceServer command (?). The protocol of activation SHALL be the responsibility of the Device Server, following this sequence: (?)
* Check no other Client owns an active and valid DeviceLock on the requested Device
* Activate the Device Lock 
* Increase the counter of Device Lock (See Below).
* Case of Forwarded Attribute(?): a Device Lock request SHALL be sent and validated by all Devices connected to the Forwarded Attributes of the targetted Device. 
In case of failure of the activation of a Device Lock the Device Server SHALL throw a (LOCKING?) Tango Exception.
A Locking Exception is an Exception with the id "API_DeviceLocked".

A Client owning a Device Lock can:
* revoke the Device Lock by the DeviceServer command(?)
* renew the Device Lock activation by the DeviceServer command (in the code lock and relock seem to be from the same effect apart that lock does not need an active lock?)
A Locking Tango Exception SHALL be thrown if the Device Lock is not active anymore


A Device Lock is defined by:
* a Client Identification (TODO specify in this RFC) of the owner
* the Client Identification of the previous owner (Mandatory?)
* a timestamp corresponding of the locking activation
* a counter (?) representing the number of valid activation from the same Client 
* the activation time in second defining the locking period from the last activation timestamp  # time_t in the implementation?)

The desactivation  of a Device Lock is defined by these conditions:
* Time of activation expired
* The Connection to the Client is lost
* The Device Server is reinitialised 
* ... TODO

A Client Identication SHOULD cary these information:
* Hostname where the Client process runs
* PID of the Client process
* IP of the Client connection
It is recommended to include any other informations which may help the administrators of the system.  



### Connection management

* The Reqest-Reply protocol SHALL manage client connection.

#### Connection establishement

#### Serving multiple devices 

#### Serving for multiple client connections

#### Staled connections

#### Transparent reconnection on client side
