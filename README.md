# Tango Request For Comments (RFC)

This repository is the home of all Tango Open Specification.

## Mission

The goal of this RFC project is to provide a formal specification of the current Tango Controls system. This specification shall include:

1. concepts
2. terminology, 
3. protocol behaviour,
4. conventions,
5. common APIs,
6. common utilities,

each on a sufficient level for preparation of specification of a new vesrion of Tango Controls (Tango v10). In that respect concepts are more important than details.

When the time comes to write v10, we will make a copy the v9 RFC and start discussing what to keep, what to remove and what to add.

## Contribution

The process to add or change an RFC is the following:

- An RFC is created and modified by pull requests according to the Collective Code Construction Contract [(C4)](https://github.com/unprotocols/rfc/blob/master/1/README.md).
- The RFC life-cycle SHOULD follow the life-cycle defined in the Consensus-Oriented Specification System [(COSS)](https://github.com/unprotocols/rfc/blob/master/2/README.md).

Read more [here](https://github.com/tango-controls/rfc/wiki).

## RFCs

The table below summarises all available or expected specifications. For the current "work in progress status", please 
check either PRs or [Wiki](https://github.com/tango-controls/rfc/wiki/Work-in-Progress-RFCs).  

Short Name               | Title                                                         | Status     | Editor
-------------------------|---------------------------------------------------------------|------------|-------
RFC-1                    | The Tango control system                                      | Raw        | Lorenzo Pivetta
[RFC-2](2/Device.md)     | The device object model                                       | Draft      | Vincent Hardion
[RFC-3](3/Command.md)    | The command model                                             | Draft      | Sergi Blanchi-Torné
[RFC-4](4/Attribute.md)  | The attribute model                                           | Draft      | Sergi Blanchi-Torné
[RFC-5](5/Property.md)   | The property model                                            | Draft      | Gwenaelle Abeillé
[RFC-6](6/Database.md)   | The database system                                           | Draft      | Gwenaelle Abeillé
RFC-7                    | The pipe model                                                | Raw        | Reynald Bourtembourg
RFC-8  | The server model                                               | Raw          | Lorenzo Pivetta
RFC-9  | Data types                                                     | Raw          | Gwenaelle Abeillé
RFC-10 | The Request-Reply protocol                                     | Raw          | Reynald Bourtembourg
RFC-11 | The Request-Reply protocol - CORBA implementation              | Raw          | 
RFC-12 | The Publisher-Subscriber protocol                              | Raw          | Vincent Hardion 
RFC-13 | The Publisher-Subscriber protocol - ZeroMQ implementation      | Raw          | 
[RFC-14](14/Logging.md) | Logging service                                                | Raw          | Sergi Blanchi-Torné
RFC-15 | The dynamic attribute and command                              | Raw          | Reynald Bourtembourg
RFC-16 | Cache system                                                   | Raw          | 
RFC-17 | Memorised attribute service                                    | Raw          | 
RFC-18 | Authorisation system                                           | Raw          | 
RFC-XX | High Level API                                                 | Raw          | 
RFC-XX | High Level API - Python implementation                         | Raw          | 
RFC-XX | High Level API - Java   implementation                         | Raw          | 
