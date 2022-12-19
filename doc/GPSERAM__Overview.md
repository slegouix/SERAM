GP SERAM - Protocol Overview
============================

This specification defined a protocol that allows a *Device Application*
to request the management of a *Secure Element* by a *Management Platform*.

The following figure present the generic architecture of GP SERAM and
its environment. All part in blue is defined in this specification.

![Architecture Overview](images/image4.png)

In this architecture, a *Remote Agent* and a *Local Agent* manage the
protocol to allow the *Management Platform* to interact with a *Device
Application* and the *Secure Element*.

While *Management Platform*, *Device* and *Secure Element* are physical
entities, the *Device Application* and *Core Management Platform* are
logical components, and the *Remote Agent* and the *Local Agent* in this
specification are to be considered as functions. These functions might
be implemented as standalone applications or as libraries integrated
inside larger business applications. In the GP SERAM context, the
terms *Remote Agent* and *Local Agent* may be used interchangeably with
*Remote Agent function* and *Local Agent function*.

Note that if the *Device* embeds a *Trusted Execution Environment*, the
*Local Agent* may be implemented in this *Trusted Execution Environment*.
Otherwise, the *Local Agent* is implemented in the *Rich Execution
Environment* of the *Device*. In these two cases, the *Local Agent* will have
to use an API providing access to the *Secure Element* and allowing it to
forward commands to an application in this *Secure Element*. This API is
called an *SE Access API* in this specification. The *SE Access API* can
be the [SIMalliance GlobalPlatform Open Mobile API](https://globalplatform.org/specs-library/open-mobile-api-specification-v3-3/)
when the *Local Agent* is implemented in the *Rich Execution Environments*
(REE), and the [GlobalPlatform TEE Secure Element API](https://globalplatform.org/specs-library/tee-secure-element-api/)
when the *Local Agent* is implemented in the *Trusted Execution Environment*
(TEE).

More precisely, an *SE Access API* usually provides mechanisms for a
device application to open a connection with the *Secure Element* and then
to open a logical channel with a card application in order to send *APDUs*
to this application.

Protocol design
---------------

### Protocol Layers

GP SERAM is a message-oriented protocol which used the following
protocol stack:

![Protocol layers](images/image5.png)

*Messages* are the data exchanged between the *Remote Agent* and the *Local
Agent*. The definition, the meaning and sequency of *Messages* are states
below in the [Messages section](#messages).

How *Messages* are carried on the network relies on the *Protocol Binding*
used by the *Remote Agent* and the *Local Agent*. A *Protocol Binding* defines
the rules to map *Messages* on the *Data Format* layer and the *Transport*
layer.

This version of the specification defined the following *Data Format* and
*Transport* protocol layers:

-   *HTTPS* as *Transport*

-   *JSON* as *Data Format*

Others protocol layer elements may be defined in the future.

This version of the specification defined the following *Protocol
Binding*:

-   *HTTP REST*, which uses *HTTPS* as *Transport Protocol* and *JSON* as *Data
    Format*.

Others *Protocol Binding* may be defined in the future.

### Management Session

To synchronize the management process, a *Device Application* and a
*Management Platform* relies on a session identifier, named *sessionId*. The
*sessionId* is shared and used for all communications between the *Remote
Agent* and the *Remote Agent*.

The timing frame during which it is performed some management tasks that
are associated with one sessionId is called the *Management Session*.

![Management Session](images/image6.png)

The *Management Session* is started by the *Device Application* and is then
controlled until its end by the *Remote Agent*.

### Protocol Steps

As shown by the next figure, the protocol has three steps. First the
*Local Agent* is triggered by the *Device Application* with the *sessionId*
and the *RA Endpoint*. It then negotiates the *Data Format* and *Transport
protocol to use with the *Remote Agent*. Finally, the *Remote Agent* make
the task required for the *Management Session*.

![Protocol Steps](images/image7.png)

Most of the *Initialization Step* is out of the scope of this document.

During the *Handshake Step*, the *Local Agent* perform a protocol
negotiation with the *Remote Agent*, by sending its capabilities, the
session identifier, and a list of manageable *Secure Element*. A *Protocol
Binding* to be used for the next step is then selected.

Next, the *Management Platform* starts a *Remote Management Session* Step by
sending a *Start Command Message*. During the *Remote Management Session*,
the *Management Platform* may send *APDUs* to the *Secure Element*, or send
notifications to the *Device Application*. The end of the *Management
Session* is announced by a *Stop Command Message* form the *Management
Platform*.

Protocol Steps details
----------------------

### Initialization Step

During this phase, a session identifier, named *sessionId*, SHALL be
generated and shared between the *Device Application* and the *Management
Platform*. How this *sessionId* is generated and shared is out of the scope
of this specification.

The *sessionId* SHALL be a unique identifier for the *Device Application*
and the *Management Platform*. How this uniqueness is handled is out of
the scope of this specification.

Moreover, the *RA Endpoint* to communicate with the *Remote Agent* MUST also
has been shared between entities. How this *RA Endpoint* is defined and
shared is out of the scope of this specification.

GP SERAM session starts after the *Local Agent* is triggered with the
*sessionId* and the *RA Endpoint* of the *Remote Agent*. The *Local Agent*
starts with the *Handshake Step*.

### Handshake Step

As soon as it was triggered, the *Local Agent* shall start the *Handshake
Step*.

Handshaking allows a *Local Agent* and *Remote Agent* to negotiate the *Data
Format* and the *Transport* protocol used to perform the remote management.

This method shall be used by the *Local Agent* to tell which *Data Formats*
and which *Transport* protocols it supports. In response, the *Remote Agent*
shall tell which ones it selects.

The attributes which are negotiate are:

-   The secure elements: the *Local Agent* provides a list of *Secure
    Element* which may be targeted by the *Remote Agent*

-   The *Data Format* (e.g. *JSON*)

-   The *Transport Protocols* (e.g. *HTTPS*)

-   The version of the protocol

To allow the *Remote Agent* to track the *Remote Management Session* the
*Local Agent* provides a *sessionId* that shall be reused in all subsequent
exchanges as defined by the selected *Protocol Binding*.

The *Handshake Step* shall be performed using the *HTTP REST Protocol
Binding*.

### Remote Management Session Step

During the *Remote Management Session Step*, the *Remote Agent* performs a
series of actions by sending *Command Messages* to the *Local Agent*.
*Messages* which are exchanges during this *Remote Management Session Step*
shall use the *Protocol Binding* which has been selected during the
*Handshake Step*.

![Remote Agent processing during Remote Management Session](images/image8.png)

As shown by the diagram, the first and last *Message* exchanged during the
*Remote Management Session Step* is respectively the *RAM Start Command* and
*RAM Stop Command*. Other *RAM Commands* are used to send *APDUs* to the
*Secure Element*, or *Notifications* to the *Device Application*. Only the
*RAM Start* and *RAM Stop Commands* are mandatory.

The *Local Agent* processed each *Command* and if required send a *Response
Message*.

Protocol overview diagram
-------------------------

The following sequence diagram resume the main exchanges during a
*Management Session*. In the diagram the exchanges during the *Remote
Management Session Steps* are illustrated with the two types of *RAM
Command* that can be sent by a *Remote Agent* after the *RAM Start Command*
*Message* and until the *RAM Stop Command Message*.

![Protocol overview](images/image9.png)

Messages
========

The following type of *Messages* may be exchanges between *Local Agent* and
*Remote Agent*:

| Protocol Step     | Messages                 | From         | To           |
|-------------------|--------------------------|--------------|--------------|
| Handshake         | **Handshake Command**    | Local Agent  | Remote Agent |
| Handshake         | **Handshake Response**   | Remote Agent | Local Agent  |
| Remote Management | **RAM Start Command**    | Remote Agent | Local Agent  |
| Remote Management | **SE RAM Command**       | Remote Agent | Local Agent  |
| Remote Management | **SE RAM Response**      | Local Agent  | Remote Agent |
| Remote Management | **Notification Command** | Remote Agent | Local Agent  |
| Remote Management | **RAM Stop Command**     | Remote Agent | Local Agent  |

The next figure details the *Messages* state machine:

![Messages State Machine](images/image10.png)
