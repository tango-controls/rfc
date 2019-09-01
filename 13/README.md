---
domain: rfc.tango-controls.org 
shortname: 13/Pub-Sub_ZeroMQ_impl
name: Publisher-Subscriber protocol - ZeroMQ implementation 
status: raw 
editor:  
contributors: Igor Khokhriakov `<at hzg.de - igor.khokhriakov>`, Olga Merkulova
---

This document describes the Publisher-Subscriber protocol - ZeroMQ implementation.

## Preamble

Copyright (c) 2019 Tango Controls

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version. This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

This Specification is a [free and open standard](http://www.digistan.org/open-standard:definition) and is governed by the Digital Standards Organization's [Consensus-Oriented Specification System](http://www.digistan.org/spec:1/COSS).

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Tango Controls Specification of Publisher-Subscriber protocol - ZeroMQ implementation 

The Purpose of this specification is to describe ZMQ implementation of Publisher-Subscriber protocol.

The ZMQ implementation has internal API components which allow to implement Publisher-Subscriber protocol. The most important methods and variables used in ZMQ implementation were mentioned in Definitions part.

The specification is divided by internal API components with their major methods and with the respect to the client and to the server parts.


### Goals

Our goal was to analyse Java, C++ code and to understand initial intentions of developers. We had to highlight the significant parts and understand logic to describe the structure of ZMQ implementation of Publisher-Subscriber protocol.


#### API Components

Here are listed kernel library [LINK:RFC-1 -- ???] components that provide API to Tango Event System to clients

1. AttributeProxy [LINK:RFC-4] -- en entry point for client code to Tango Event System  

   ```
   subscribe_event(Event, [Optional]Callback, List:Filters):EventId
   ``` 

2. DeviceProxy [LINK:RFC-2] -- same as 1. but on a device level 

   ```
   subscribe_event(AttributeName, Event, [Optional]Callback, List:Filters):EventId
   ```

#### Internal API Components

The Tango Event System API consists of the following components:

1. EventCallBackStruct
2. EventChannelStruct
3. EventConsumer
4. ZmqEventConsumer
5. KeepAliveThread
6. ZmqMainThread
7. ZmqUtils
8. TODO: server side

*EventCallBackStruct* -- defines structure of event Callback having device, attribute mane, channel name, etc.

*EventChannelStruct* -- defines structure of event channel having admin device proxy, last heartbeat, last subscribed, host, database, ect.

*EventConsumer* -- client implementation of pub-sub protocol [RFC-12]

*ZmqEventConsumer* -- encapsulates ZMQ specific logic of *EventConsumer*

*ZmqMainThread* -- manages ZMQ event receptions

*KeepAliveThread* -- responsible for `resubscribe_event`

*ZmqUtils* -- set of ZMQ low level utilities


#### Definitions

EVENT_TYPE = [LINK:RFC-12#definitions]

HEARTBEAT_ENDPOINT = "(upstream server FQDN):port"  

EVENT_ENDPOINT = "(upstream server FQDN):port"

HEARTBEAT_CHANNEL = "(upstream admin device FQDN)/heartbeat" 

EVENT_CHANNEL = "(upstream device FQDN)/(attr).[idl5_]EVENT_TYPE

Subscription -- _noun_: a pair event_channel_name -> event_callback function


__Constants:__

`heartbeat_period` -- in Tango 9 it is 10s.


__Variables:__

`mcast` -- multicast protocol

`[client/server]_[heartbeat/event]_endpoint` -- ZMQ connection point e.g. `tcp://host:port`
last subscribed

`[heartbeat/event]_channel` -- virtual connection channel between client and server

`[heartbeat/event]_channel_name` -- a String value that represents channel i.e. ZMQ topic

`event_callback` -- a function that is called whenever client receives corresponding event

`control_socket` -- ZMQ `inproc:` endpoint 

`server_heartbeat_socket` -- ZMQ PUB socket bound to a `server_heartbeat_endpoint`

`server_event_socket` -- ZMQ PUB socket bound to a `server_event_endpoint`

`client_heartbeat_socket` -- ZMQ SUB socket connected to a `server_heartbeat_endpoint`

`client_event_socket` -- ZMQ SUB socket connected to a `server_event_endpoint`

`failed_event_callback_map` - all events with their structures are put here if they failed to connect and `stateless` == "false"

`stateless` -- an API parameter that indicates client interests in failing fast whenever Tango Event System connection has failed during `subscribe_event`


__Methods:__

`subscribe_event` -- TODO

`resubscribe_event` -- TODO

`unsubscribe_event` -- TODO

`connect_event` -- methods that encapsulate low level Tango Event System [LINK:RFC-1] connection management

`disconnect_event` -- TODO

`disconnect_channel` -- TODO

`manage[*]` -- typical 'manage'-method takes low level data, transforms it into high-level data and passes control further

`check[*]` -- TODO


## Specification

### AttributeProxy

```cpp
AttributeProxy->subscribe_event(): EventId
AttributeProxy->unsubscribe_event(EventId)
```

#### subscribe_event

1. Client MUST have a valid reference to ApiUtil[??? RFC-11] to perform event subscription

   > As per Tango 9<= kernel library implementation heavily relies on static helpers located in ApiUtil component [??? RFC-11]
   
2. Client SHOULD lookup for existing [ZmqEventConsumer](#Internal_API_Components) instance otherwise client creates it 

   > ZmqEventConsumer is implemented as a singleton in Tango 9 C++/Java kernel libraries
   
3. Client MAY proceed with NotifdEventConsumer [RFC-??? - PUB/SUB Notifd Implementation] if step 2. fails

4. Client SHOULD delegate `subscribe_event` to [EventConsumer](#Internal_API_Components)

   > AttributeProxy may simply delegate `subscribe_event` to [DeviceProxy](#API_Components)  

#### unsubscribe_event

1. Client SHOULD delegate `unsubscribe_event` to EventConsumer



### EventConsumer

```cpp
EventConsumer->subscribe_event():EventId
EventConsumer->connect_event():
EventConsumer->unsubscribe_event(EventId)
EventConsumer->disconnect_event(event_channel_name, event_endpoit):void
EventConsumer->disconnect_channel(event_channel_name, event_channel_endpoint, event_endpoit):void
```

#### subscribe_event


1. Client MUST throw exception if Event is QUALITY_EVENT [LINK:RFC-12#definitions]
 
   > Tango 9 does not support QUALITY_EVENT [LINK:RFC-12#definitions] anymore
   
2. Client MUST prevent dead lock in case `subscribe_event` is called from an `event_callback` of another [Subscription](#Glossary)

   > To achieve this client may `subscribe` from a different thread later on

3. Client MUST postpone `subscribe_event` if `connect_event` has failed and `stateless` is **true**

   > client puts newly created [EventCallbackStruct](#Glossary) into `failed_event_callback_map` and iterates over it later to perform `subscribe_event` again
   
4. Client ____ delegates `EventConsumer::connect_event` to perform low level connection

#### connect_event   

1. Client MUST handle INTERFACE_CHANGE [LINK:RFC-12#definitions] and PIPE [LINK:RFC-12#definitions]

   > in Tango 9 this is done via setting corresponding **bool** fields and checking set values later on
 
2. Client SHOULD create `event_callback_key`[TODO: check if is the same as `event_channel_name`] 
 
   > the following must be taken into account: actual Tango host value [RFC-1]; nodb mode [RFC-1]; EVENT_TYPE [RFC-12] 
   
3. Client MUST handle compatibility

   > in Tango 9 server inserts `_idl5` into `event_channel_name` [RFC-12]
   
4. Client MUST call admin's [RFC-2] ZmqEventSubscriptionChange command [RFC-2, RFC-3]
 
   > in order to execute command on target admin device, client must have its DeviceProxy reference
   > client prepares DeviceData and executes ZqmEventSubscriptionChange command of the target device's admin device
   > in return target admin device returns essential low level data: endpoints, channel names etc [see Glossary](#Glossary) 

6. Client SHOULD check received in step 4 endpoints for availability and connect to corresponding `[heartbeat|event]_endpoint`s

   > client creates `[heartbeat|event]_socket`s and connect them to the endpoints 

7. Client MUST use received in step 4 `heartbeat_channel_name` and `event_channel_name` as ZMQ SUB topics

   > prior Tango 9.3.3 client guessed `heartbeat_channel_name` and `event_channel_name` by "manually" constructing them

8. Client MAY invoke `event_callback` simulating first event

9. Client MUST handle ZMQ specific features

   > Since Tango 8.1.0 `heartbeat_channel_name` is lower cased by the upstream device
   > Check for ZMQ server/client versions compatibility
   > Check if `mcast` is used and if it is supported by client ZMQ library. 
   > Otherwise throw an exception
   
10. Client SHOULD handle forwarded attributes accordingly [RFC-XX]

11. Client MUST create [EventCallbackStructure](#Internal_API_components)  i.e. create Subscription and put it into `event_callback_map`

12. Client MUST assign an EventId for newly created Subscription and return it to its client

#### unsubscribe_event

1. Client MUST execute ZmqEventSubscriptionChange command [RFC-2, RFC-3] with action == "unsubscribe"

1. Client MUST remove EventCallbackStructure from `event_callback_map`, `failed_event_callback_map`

   > keep concurrent modifications in mind 

2. Client SHOULD `disconnect_event`

3. Client SHOULD `disconnect_channel` if no other EventCallbackStructure uses the same `event_channel` 


#### disconnect_event

1. Client MUST disconnect `client_event_socket`
 
   > done by setting ZMQ `ZMQ_UNSUBSCRIBE` socket option
   > `mcast` doesnt require this step

2. Client SHOULD `unsubscribe_event` from every Tango host


#### disconnect_channel 

1. Client MUST disconnect `client_heartbeat_socket`

   > same as in `disconnect_event`
   > in case client ZMQ library has disconnected - client may call it


### KeepAliveThread

#### Resubscribe

Resubscribe happens in a dedicated background thread - [KeepAliveThread](#Glossary)

1. Client SHOULD try to `subscribe_event` to not connected events 

   > client may perform `subscribe_event` each `heartbeat_period`
   
2. Client SHOULD loop through `failed_event_callback_map` and `subscribe_event`

3. Client MUST confirm its subscription by calling ConfirmSubscription command [RFC-3] on upstream admin device [RFC-2,3] 

   > Tango 9 kernel library implementation uses ZmqEventSubscriptionChange command [RFC-2, RFC-3] to confirm its subscription
 
4. Client MUST try to `subscribe_event` if HEARTBEAT [RFC-12] is missing from upstream admin device  
