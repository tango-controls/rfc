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

TODO discuss what from Tango Event System API should be included. 

Our goal was to analyse Java, C++ code and to understand initial intentions of developers. We had to highlight the significant parts and understand logic to describe the structure of ZMQ implementation of Publisher-Subscriber protocol.


#### Publisher-Subscriber Tango Event System API Components

The Tango Event System API consists of the following components:

*Subscriber* -- client side implementation of pub-sub protocol [RFC-12]
*Publisher* -- server side implementation of pub-sub protocol [RFC-12]


#### Definitions

EVENT_TYPE = [LINK:RFC-12#definitions]

HEARTBEAT_ENDPOINT = "(upstream server FQDN):port"  

EVENT_ENDPOINT = "(upstream server FQDN):port"

HEARTBEAT_CHANNEL = "(upstream admin device FQDN)/heartbeat" 

EVENT_CHANNEL = "(upstream device FQDN)/(attr).[idl5_]EVENT_TYPE


__Constants:__

`heartbeat_period` -- in Tango 9 it is 10s.


__Variables:__

`mcast` -- multicast protocol

`[client/server]_[heartbeat/event]_endpoint` -- ZMQ connection point e.g. `tcp://host:port`
last subscribed

`[heartbeat/event]_channel` -- virtual connection channel between client and server

`[heartbeat/event]_channel_name` -- a String value that represents channel i.e. ZMQ topic

`control_socket` -- ZMQ `inproc:` endpoint 

`server_heartbeat_socket` -- ZMQ PUB socket bound to a `server_heartbeat_endpoint`

`server_event_socket` -- ZMQ PUB socket bound to a `server_event_endpoint`

`client_heartbeat_socket` -- ZMQ SUB socket connected to a `server_heartbeat_endpoint`

`client_event_socket` -- ZMQ SUB socket connected to a `server_event_endpoint`

`stateless` -- an API parameter that indicates client interests in failing fast whenever Tango Event System connection has failed during `subscribe_event`


## Specification

 Format of this specification is the following: 

1. rule

> comment

Comments are related to Tango 9 if not explicitly mentioned otherwise.

### Subscriber

```cpp
Subscriber->subscribe_event():EventId
Subscriber->connect_event():
Subscriber->unsubscribe_event(EventId)
Subscriber->disconnect_event(event_channel_name, event_endpoit):void
Subscriber->disconnect_channel(event_channel_name, event_channel_endpoint, event_endpoit):void
Subscriber->resubscribe_event():EventId
Subscriber->listen():void
Subscriber->on_event(EventData):void
Subscriber->on_error(Error):void
```

> In Tango 9 Subscriber has a reference to ApiUtil[??? RFC-11] to perform event subscription. As per Tango 9<= kernel library implementation heavily relies on static helpers located in ApiUtil component [??? RFC-11]


1. Subscriber MUST export the following API to be compatible with Tango 9:

   1. AttributeProxy [LINK:RFC-4] -- en entry point for client code to Tango Event System
   2. SubscriberCallback -- entry point for client code that allows to react on Tango Events or Errors [RFC-12]
   3. DeviceProxy [LINK:RFC-2] -- same as 1. but on a device level

#### subscribe_event

1. Subscriber MAY delegate ZMQ specific logic to a dedicated entity

   > ZmqEventConsumer -- encapsulates ZMQ specific logic of Subscriber

   > Subscriber lookups for existing ZmqEventConsumer instance otherwise client creates it 

   > ZmqEventConsumer is implemented as a singleton in Tango 9 C++/Java kernel libraries
   
   > Subscriber proceeds with Notifd [RFC-??? - PUB/SUB Notifd Implementation] if step 1 fails

1. Subscriber MAY save data from SUBSCRIPTION_INFO [RFC-12] in an internal structure(s)
 
   > In Tango 9 that internal structures are `EventCallbackStruct` and `EventChannelStruct`.
   
   > EventCallBackStruct -- defines structure of event Callback having device, attribute name, channel name, etc.
   
   > EventChannelStruct -- defines structure of event channel having admin device proxy, last heartbeat, last subscribed, host, database, ect.
   
   > `EventCallbackStruct` is saved in `event_callback_map` if `connect_event` succeed and `failed_event_callback_map` otherwise
   
   > `failed_event_callback_map` - all events with their structures are put here if they failed to connect and `stateless` == "false"

1. Subscriber MUST throw exception if Event is QUALITY_EVENT [LINK:RFC-12#definitions]
 
   > Tango 9 does not support QUALITY_EVENT [LINK:RFC-12#definitions] anymore
   
2. Subscriber MUST prevent dead lock in case `subscribe_event` is called from another `SubscriberCallback::on_event`

   > To achieve this Subscriber may `subscribe` from a different thread later on

3. Subscriber MUST postpone `subscribe_event` if `connect_event` has failed and `stateless` is **true**

   > Subscriber puts newly created EventCallbackStruct into `failed_event_callback_map` and iterates over it later to perform `subscribe_event` again
   

#### connect_event   

> `connect_event` performs low level logic to establishes a connection

1. Subscriber MUST handle INTERFACE_CHANGE [LINK:RFC-12#definitions] and PIPE [LINK:RFC-12#definitions]

   > in Tango 9 this is done via setting corresponding **bool** fields and checking set values later on
 
2. Subscriber SHOULD create `event_callback_key`[TODO: check if is the same as `event_channel_name`] 
 
   > the following must be taken into account: actual Tango host value [RFC-1]; nodb mode [RFC-1]; EVENT_TYPE [RFC-12] 
   
3. Subscriber MUST handle compatibility

   > in Tango 9 server inserts `_idl5` into `event_channel_name` [RFC-12]
   
4. Subscriber MUST call admin's [RFC-2] ZmqEventSubscriptionChange command [RFC-2, RFC-3]
 
   > in order to execute command on target admin device, client must have its DeviceProxy reference
   > client prepares DeviceData and executes ZqmEventSubscriptionChange command of the target device's admin device
   > in return target admin device returns essential low level data: endpoints, channel names etc [see Glossary](#Glossary) 

6. Subscriber SHOULD check received in step 4 endpoints for availability and connect to corresponding `[heartbeat|event]_endpoint`s

   > client creates `[heartbeat|event]_socket`s and connect them to the endpoints 

7. Subscriber MUST use received in step 4 `heartbeat_channel_name` and `event_channel_name` as ZMQ SUB topics

   > prior Tango 9.3.3 client guessed `heartbeat_channel_name` and `event_channel_name` by "manually" constructing them

8. Subscriber MAY invoke `SubscriberCallback::on_event` simulating first event

9. Subscriber MUST handle ZMQ specific features

   > Since Tango 8.1.0 `heartbeat_channel_name` is lower cased by the upstream device
   > Check for ZMQ server/client versions compatibility
   > Check if `mcast` is used and if it is supported by client ZMQ library. 
   > Otherwise throw an exception
   
10. Subscriber SHOULD handle forwarded attributes accordingly [RFC-XX]

12. Subscriber MUST assign an EventId for newly created Subscription and return it to its client

#### unsubscribe_event

1. Subscriber MUST execute ZmqEventSubscriptionChange command [RFC-2, RFC-3] with action == "unsubscribe"

1. Subscriber MAY forget SUBSCRIPTION_INFO [RFC-12] 

   > In Tango 9 remove `EventCallbackStruct` from `event_callback_map`, `failed_event_callback_map`. 
   
   > Keep concurrent modifications in mind 

2. Subscriber SHOULD `disconnect_event`

3. Subscriber SHOULD `disconnect_channel` if no other SUBSCRIPTION_INFO [RFC-12] uses the same `event_channel` 


#### disconnect_event

1. Subscriber MUST disconnect `client_event_socket`
 
   > done by setting ZMQ `ZMQ_UNSUBSCRIBE` socket option
   > `mcast` doesnt require this step

2. Subscriber SHOULD `unsubscribe_event` from every Tango host


#### disconnect_channel 

1. Subscriber MUST disconnect `client_heartbeat_socket`

   > same as in `disconnect_event`
   > in case client ZMQ library has disconnected - client may call it


#### resubscribe_event

1. Subscriber MAY use a dedicated background thread responsible for `resubscribe_event` 

1. Subscriber SHOULD try to `subscribe_event` to not connected events 

   > Subscriber performs `subscribe_event` each `heartbeat_period`
   
   > Subscriber loops through `failed_event_callback_map` and `subscribe_event`

3. Subscriber MUST confirm its subscription by calling ConfirmSubscription command [RFC-3] on upstream admin device [RFC-2,3] 

   > Tango 9 kernel library implementation uses ZmqEventSubscriptionChange command [RFC-2, RFC-3] to confirm its subscription
 
4. Subscriber MUST try to `subscribe_event` if HEARTBEAT [RFC-12] is missing from upstream admin device  

#### listen


TODO control socket etc

#### on_event

1. Subscriber MUST call `SubscriberCallback::on_event` whenever it receives corresponding event

#### on_error

1. Subscriber MUST call `SubscriberCallback::on_error` whenever it receives corresponding event with an error


### Publisher

```cpp
Publisher->initialize():void
Publisher->subscribe(SUBSCRIBER_INFO):
Publisher->push_event(AttributeName, Event):
Publisher->close():void
```

TODO Event -> EVENT_TYPE 

1. Publisher MUST export the following API to be compatible with Tango 9:

   4. EventManager -- server side entry point to Tango Event System

#### initialize

1. Publisher MUST define endpoints 

   > publisher finds random free ports for the endpoints, typically port _n_ is alloacted to heartbeat and _n_ + 1 to event 

2. Publisher MUST `bind` to at least two endpoints: event and heartbeat

   > This also implies creation of `ZMQ::Socket`s for each endpoint
   
   > Here `ZMQ::Socket::bind` is meant

3. Publisher MAY `bind` to more than one pair of endpoints

4. Publisher SHOULD define subscriber and publisher sides High Water Marks

5. Publisher MUST schedule HEARTBEAT_EVENT at `heartbeat_period`

#### subscribe

1. Publisher MUST send Subscriber SUBSCRIPTION_INFO [RFC-12]

#### push

1. Publisher MUST check if EVENT_TYPE must be fired

2. Publisher MUST serialize EVENT_TYPE into CORBA message

3. Publisher MUST send EVENT_TYPE via `event_endpoint` using `event_channel_name` as ZMQ topic

4. Publisher SHOULD propagate any exception to client code

#### close

1. Publisher MUST disconnect from ZMQ sockets and free all resources