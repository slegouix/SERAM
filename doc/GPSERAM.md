GlobalPlatform Technology

Secure Element Remote Application Management

Version 2.0.0

Draft

may 2022

Document Reference: GPD\_SPE\_008

THIS SPECIFICATION OR OTHER WORK PRODUCT IS BEING OFFERED WITHOUT ANY
WARRANTY WHATSOEVER, AND IN PARTICULAR, ANY WARRANTY OF NON‑INFRINGEMENT
IS EXPRESSLY DISCLAIMED. ANY IMPLEMENTATION OF THIS SPECIFICATION OR
OTHER WORK PRODUCT SHALL BE MADE ENTIRELY AT THE IMPLEMENTER’S OWN RISK,
AND NEITHER THE COMPANY, NOR ANY OF ITS MEMBERS OR SUBMITTERS, SHALL
HAVE ANY LIABILITY WHATSOEVER TO ANY IMPLEMENTER OR THIRD PARTY FOR ANY
DAMAGES OF ANY NATURE WHATSOEVER DIRECTLY OR INDIRECTLY ARISING FROM THE
IMPLEMENTATION OF THIS SPECIFICATION OR OTHER WORK PRODUCT.

This document has been made available for Member Review before
completion to facilitate early review and feedback by other
GlobalPlatform Committees and external partners. It is an unpublished
work-in-progress

Contents

[1 Introduction 7](#_Toc103330501)

[1.1 Audience 7](#audience)

[1.2 IPR Disclaimer 7](#ipr-disclaimer)

[1.3 References 7](#references)

[1.4 Terminology and Definitions 8](#_Toc103330505)

[1.5 Abbreviations and Notations 9](#abbreviations-and-notations)

[1.6 Revision History 9](#_Toc103330507)

[2 Protocol 10](#protocol)

[2.1 Protocol design 10](#protocol-design)

[2.1.1 Protocol Layers 10](#protocol-layers)

[2.1.2 Management Session 11](#management-session)

[2.1.3 Protocol Steps 12](#protocol-steps)

[2.2 Protocol Steps details 13](#protocol-steps-details)

[2.2.1 Initialization Step 13](#initialization-step)

[2.2.2 Handshake Step 13](#handshake-step)

[2.2.3 Remote Management Session Step
13](#remote-management-session-step)

[2.3 Protocol overview diagram 14](#protocol-overview-diagram)

[3 Messages 16](#_Toc103330518)

[3.1 Handshake Command Message 17](#handshake-command-message)

[3.1.1 Message description 17](#message-description)

[3.1.2 Message Properties 17](#message-properties)

[3.2 Handshake Response Message 18](#handshake-response-message)

[3.2.1 Message description 18](#message-description-1)

[3.2.2 Message Properties 18](#message-properties-1)

[3.2.3 Message State Machine 19](#message-state-machine)

[3.3 RAM Start Command Message 19](#ram-start-command-message)

[3.3.1 Message description 19](#message-description-2)

[3.3.2 Message Properties 19](#message-properties-2)

[3.4 SE RAM Command Message 19](#se-ram-command-message)

[3.4.1 Message description 19](#message-description-3)

[3.4.2 Message Properties 19](#message-properties-3)

[3.5 SE RAM Response Message 20](#se-ram-response-message)

[3.5.1 Message description 20](#message-description-4)

[3.5.2 Message Properties 20](#message-properties-4)

[3.6 Notification Command Message 20](#notification-command-message)

[3.6.1 Message description 20](#message-description-5)

[3.6.2 Message Properties 20](#message-properties-5)

[3.7 RAM Stop Command Message 21](#ram-stop-command-message)

[3.7.1 Message description 21](#message-description-6)

[3.7.2 Message Properties 21](#message-properties-6)

[4 HTTP REST Binding 22](#http-rest-binding)

[4.1 Management Session and HTTP messages
22](#management-session-and-http-messages)

[4.2 HTTP Polling 22](#http-polling)

[4.3 Message Pipelining 23](#message-pipelining)

[4.4 HTTP REST Endpoints 23](#http-rest-endpoints)

[4.5 Binding of the Handshake Step 24](#binding-of-the-handshake-step)

[4.6 Binding of the Remote Management
25](#binding-of-the-remote-management)

[4.6.1 Binding of the HTTP request to the Remote Management Endpoint
25](#binding-of-the-http-request-to-the-remote-management-endpoint)

[4.6.2 SE RAM Response Binding 25](#se-ram-response-binding)

[4.6.3 Binding of the HTTP Response for Remote Management Endpoint
25](#binding-of-the-http-response-for-remote-management-endpoint)

[4.6.4 RAM Start Command binding 26](#ram-start-command-binding)

[4.6.5 SE RAM Command binding 26](#se-ram-command-binding)

[4.7 Transmission Errors 26](#transmission-errors)

[5 Security 28](#_Toc103330554)

[Annex A HTTP Rest Binding API Specification
29](#http-rest-binding-api-specification)

[Annex B SAM Use Case 33](#sam-use-case)

[Annex C LPA Proxy Use Case 34](#lpa-proxy-use-case)

Figures

Figure 2‑1 - Architecture Overview 11

Figure 2‑2 – Protocol layers 12

Figure 2‑3 – Management Session 13

Figure 2‑4 – Protocol Steps 13

Figure 2‑5 – Remote Agent processing during Remote Management Session 15

Figure 2‑6 – Protocol overview 16

Figure 3‑1 - Messages State Machine 18

Figure 3‑2 – HTTP polling 24

Tables

[Table 1‑1: Normative References 7](#_Toc103330566)

[Table 1‑2: Informative References 8](#_Toc103330567)

[Table 1‑3: Terminology and Definitions 8](#_Ref323039018)

[Table 1‑4: Abbreviations and Notations 9](#_Ref311381415)

[Table 1‑5: Revision History 9](#_Toc291691011)

[Table 3‑1: Types of Messages in GPSERAM 16](#_Toc103330571)

[Table 3‑2: Handshake Command properties 18](#_Toc103330572)

[Table 3‑4: Handshake Response properties 18](#_Toc103330573)

[Table 3‑5: Handshake Response Message State Machine 19](#_Toc103330574)

[Table 3‑6: RAM Start Command properties 19](#_Toc103330575)

[Table 3‑8: SE RAM Command properties 19](#_Toc103330576)

[Table 3‑10: SE RAM Response properties 20](#_Toc103330577)

[Table 3‑12: Notification Command properties 21](#_Toc103330578)

[Table 3‑18: RAM Stop Command properties 21](#_Toc103330579)

[Table 3‑20– Handshake Command binding 24](#_Ref97549956)

[Table 3‑21– Handshake Command binding 24](#_Ref97551517)

[Table 3‑22– SE RAM Response binding 25](#_Ref97565618)

[Table 3‑24– Handshake Command binding 26](#_Ref97579795)

[Table 3‑25– Handshake Command binding 26](#_Toc103330584)

Introduction
============

The aim of this document is to define an administration protocol to
perform remote management of applications residing on any type of Secure
Element in a device (e.g. mobile phone, tablet...). Remote management
means the delivery of management commands to a Secure Element hosted in
a device and the management of the related responses. These management
commands could for instance be used to install an application and to
provide data to an application. Examples of applications could be
payment applications, transit applications, or access applications.

TBC

Audience
--------

This document is intended primarily for the use of Device manufacturers
that will implement the Local Agent, Secure Element manufacturers, and
providers of remote administration solutions. This document may also be
of interest to service providers that will deliver services on Secure
Elements.

IPR Disclaimer
--------------

Attention is drawn to the possibility that some of the elements of this
GlobalPlatform specification or other work product may be the subject of
intellectual property rights (IPR) held by GlobalPlatform members or
others. For additional information regarding any such IPR that have been
brought to the attention of GlobalPlatform, please visit
<https://globalplatform.org/specifications/ip-disclaimers/>.
GlobalPlatform shall not be held responsible for identifying any or all
such IPR, and takes no position concerning the possible existence or the
evidence, validity, or scope of any such IPR.

References
----------

The tables below list references applicable to this specification. The
latest version of each reference applies unless a publication date or
version is explicitly stated.

<span id="_Toc103330566" class="anchor"></span>Table ‑: Normative
References

|                          |                                                                           |             |
|--------------------------|---------------------------------------------------------------------------|-------------|
| Standard / Specification | Description                                                               | Ref         |
| OAS                      | Open API Initiative - OpenAPI Specification version 3.0                   | \[OAS\]     |
| RFC2818                  | HTTP Over TLS                                                             | \[RFC2818\] |
| RFC 6265                 | IETF – HTTP State Management Mechanism                                    | \[RFC6268\] |
| RFC7230                  | IETF – Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing | \[RFC7230\] |
| RFC7231                  | IETF – Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content      | \[RFC7231\] |
| RFC7232                  | IETF – Hypertext Transfer Protocol (HTTP/1.1): Conditional Requests       | \[RFC7232\] |
| RFC7234                  | IETF – Hypertext Transfer Protocol (HTTP/1.1): Caching                    | \[RFC7234\] |

<span id="_Toc103330567" class="anchor"></span>Table ‑: Informative
References

|                          |                                                                                                        |             |
|--------------------------|--------------------------------------------------------------------------------------------------------|-------------|
| Standard / Specification | Description                                                                                            | Ref         |
| RFC 6202                 | IETF - Known Issues and Best Practices for the Use of Long Polling and Streaming in Bidirectional HTTP | \[RFC6202\] |
| OMAPI                    | GlobalPlatform – Open Mobile API Specification version 3.3                                             | \[OMAPI\]   |

Terminology and Definitions
---------------------------

The following meanings apply to SHALL, SHALL NOT, MUST, MUST NOT,
SHOULD, SHOULD NOT, and MAY in this document (refer to RFC 2119):

-   **SHALL** indicates an absolute requirement, as does **MUST**.

-   **SHALL NOT** indicates an absolute prohibition, as does **MUST
    > NOT**.

```{=html}
<!-- -->
```
-   **SHOULD** and **SHOULD NOT** indicate recommendations.

-   **MAY** indicates an option.

Selected terms used in this document are included in Table 1‑3.

<span id="_Ref323039018" class="anchor"></span>Table ‑: Terminology and
Definitions

|                               |                                                                                                                                                                                          |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Term                          | Definition                                                                                                                                                                               |
| Core Management Platform      | Part of the Management Platform not covered by this specification. The core management platform is responsible to create the APDU commands that need to be delivered by the Remote Agent |
| RA Endpoint                   | Base URI used by the Local Agent to makes the Handshake                                                                                                                                  |
| Command                       | Message sends by the RA during the Remote Management Step                                                                                                                                |
| Data Format                   | How data exchanged between Remote Agent and Local Agent are formatted. Defines as a MIME type.                                                                                           |
| Device                        | User equipment used in conjunction with an eUICC to connect to a mobile network. E.g. a tablet, wearable, smartphone, or handset.                                                        |
| Device Application            | Any piece of software in the Device. It may be a Device component or an external third-party application.                                                                                |
| Handshake                     | Protocol Step during which the Secure Element and the Protocol Binding to be used for the Remote Management Session is selected.                                                         |
| Initialization                | Protocol Step during which the sessionId is generated and shared                                                                                                                         |
| Management Platform           | A cloud infrastructure able to manage some Secure Element.                                                                                                                               |
| Management Session            | The timing frame during which the management task associated with a sessionId is performed.                                                                                              |
| Message                       | A dataset of Properties exchanged during a Management Session.                                                                                                                           |
| Protocol Binding              | Rules associated with a Data Format and a Transport protocol to exchange Message                                                                                                         |
| Protocol Step                 | Part of the Management Session.                                                                                                                                                          |
| Remote Agent                  | A Management Platform intermediate between Core Management Platform and the Local Agent.                                                                                                 |
| Remote Management Session     | A Protocol Step during which the Remote Platform perform the management of a Secure Element.                                                                                             |
| Response                      | Message that provides a response to a Command Message                                                                                                                                    |
| Rich Execution Environment    | As defined by GP TEE                                                                                                                                                                     |
| Secure Element                | Physical component attached with the Device manageable according with GP Card specification.                                                                                             |
| sessionId                     | An identifier shared between the Device Application and the Management Platform and associated with a Management Session                                                                 |
| Step                          | See Protocol Step                                                                                                                                                                        |
| Transport                     | The transport protocol used between Remote Agent and Local Agent.                                                                                                                        |
| Trusted Execution Environment | As defined by GP TEE                                                                                                                                                                     |
| Local Agent                   | A Device intermediate between Remote Agent and the Secure Element.                                                                                                                       |

Abbreviations and Notations
---------------------------

<span id="_Ref311381415" class="anchor"></span>Table ‑: Abbreviations
and Notations

|                         |                                       |
|-------------------------|---------------------------------------|
| Abbreviation / Notation | Meaning                               |
| URI                     | Uniform Resource Identifier           |
| UTF‑8                   | Unicode Transformation Format – 8‑bit |
| LA                      | Local Agent                           |
| RA                      | Remote Local Agent                    |
| SE                      | Secure Element                        |

Revision History
----------------

GlobalPlatform technical documents numbered *n*.0 are major releases.
Those numbered *n*.1, *n*.2, etc., are minor releases where changes
typically introduce supplementary items that do not impact backward
compatibility or interoperability of the specifications. Those numbered
*n*.*n*.1, *n*.*n*.2, etc., are maintenance releases that incorporate
errata and precisions; all non‑trivial changes are indicated, often with
revision marks.

<span id="_Toc291691011" class="anchor"></span>Table ‑: Revision History

| Date     | Version | Description     |
|----------|---------|-----------------|
| May 2011 | 0.1     | Initial release |
| Nov 2005 | 1.01    | TBC             |
| 2022     | 2.0     | TBC             |

Protocol
========

This specification defined a protocol that allows a Device Application
to request the management of a Secure Element by a Management Platform
Architecture

The following figure present the generic architecture of GP SERAM and
its environment. All part in blue is defined in this specification.

Figure ‑ - Architecture Overview

<img src="images/image4.png" style="width:5.75in;height:2.52083in" />

In this architecture, a Remote Agent and a Local Agent manage the
protocol to allow the Management Platform to interact with a Device
Application and the Secure Element.

While Management Platform, Device and Secure Element are physical
entities, the Device Application and Core Management Platform are
logical components, and the Remote Agent and the Local Agent in this
specification are to be considered as functions. These functions might
be implemented as standalone applications or as libraries integrated
inside larger business applications. In the rest of the document, the
terms Remote Agent and Local Agent may be used interchangeably with
“Remote Agent function” and “Local Agent function”.

Note that if the Device embeds a Trusted Execution Environment, the
Local Agent may be implemented in this Trusted Execution Environment.
Otherwise, the Local Agent is implemented in the Rich Execution
Environment of the device. In these two cases, the Local Agent will have
to use an API providing access to the Secure Element and allowing it to
forward commands to an application in this Secure Element. This API is
called an “SE Access API” in this specification. The SE Access API can
be the SIMalliance GlobalPlatform Open Mobile API \[OMAPI\] when the
Local Agent is implemented in the Rich Execution Environments (REE), and
the GlobalPlatform TEE Secure Element API \[GP TEE SE API\] when the
Local Agent is implemented in the Trusted Execution Environment (TEE).

More precisely, an SE Access API usually provides mechanisms for a
device application to open a connection with the Secure Element and then
to open a logical channel with a card application in order to send APDUs
to this application

Protocol design
---------------

### Protocol Layers

GP SERAM is a message-oriented protocol which used the following
protocol stack:

Figure ‑ – Protocol layers

<img src="images/image5.png" style="width:4.07292in;height:1.67708in" />

Messages are the data exchanged between the Remote Agent and the Local
Agent. The definition, the meaning and sequency of Messages are states
in section 3.1.

How Messages are carried on the network relies on the Protocol Binding
used by the Remote Agent and the Local Agent. A Protocol Binding defines
the rules to map Messages on the Data Format layer and the Transport
layer.

This version of the specification defined the following Data Format and
Transport protocol layers:

-   HTTPS as Transport

-   JSON as Data Format

Others protocol layer elements may be defined in the future.

This version of the specification defined the following Protocol
Binding:

-   HTTP REST, which uses HTTPS as Transport Protocol and JSON as data
    format.

Others Protocol Binding may be defined in the future.

### Management Session

To synchronize the management process, a Device Application and a
Management Platform relies on a session identifier, named sessionId. The
sessionId is shared and used for all communications between the Remote
Agent and the Remote Agent.

The timing frame during which it is performed some management tasks that
are associated with one sessionId is called the Management Session.

Figure ‑ – Management Session

<img src="images/image6.png" style="width:6.0625in;height:3.14583in" />

The Management Session is started by the Device Application and is then
controlled until its end by the Remote Agent.

### Protocol Steps

As shown by the next figure, the protocol has three steps. First the
Local Agent is triggered by the Device Application with the sessionId
and the RA Endpoint. It then negotiates the Data Format and Transport
protocol to use with the Remote Agent. Finally, the Remote Agent make
the task required for the Management Session.

Figure ‑ – Protocol Steps

<img src="images/image7.png" style="width:5.42708in;height:1.05208in" />

Most of the Initialization Step is out of the scope of this document.

During the Handshake Step, the Local Agent perform a protocol
negotiation with the Remote Agent, by sending its capabilities, the
session identifier, and a list of manageable Secure Element. A Protocol
Binding to be used for the next step is then selected.

Next, the Management Platform starts a Remote Management Session Step by
sending a Start Command Message. During the Remote Management Session,
the Management Platform may send APDUs to the Secure Element, or send
notifications to the Device Application. The end of the management
session is announced by a Stop Command Message form the Management
Platform.

Protocol Steps details
----------------------

### Initialization Step

During this phase, a session identifier, named sessionId, SHALL be
generated and shared between the Device Application and the Management
Platform. How this sessionId is generated and shared is out of the scope
of this specification.

The sessionId SHALL be a unique identifier for the Device Application
and the Management Platform. How this uniqueness is handled is out of
the scope of this specification.

Moreover, the RA Endpoint to communicate with the Remote Agent MUST also
has been shared between entities. How this Endpoint is defined and
shared is out of the scope of this specification.

GP SERAM session starts after the Local Agent is triggered with the
sessionId and the RA Endpoint of the Remote Agent. The Local Agent
starts with the Handshake step.

### Handshake Step

As soon as it was triggered, the Local Agent shall start the Handshake
Step.

Handshaking allows a Local Agent and Remote Agent to negotiate the Data
Format and the Transport protocol used to perform the remote management.

This method shall be used by the Local Agent to tell which Data Formats
and which Transport protocols it supports. In response, the Remote Agent
shall tell which ones it selects.

The attributes which are negotiate are:

-   The secure element: the Local Agent provides a list of Secure
    Element which may be targeted by the Remote Agent

-   The Data Format (e.g. JSON)

-   The Transport Protocols (e.g. HTTPS)

-   The version of the protocol

To allow the Remote Agent to track the Remote Management Session the
Local Agent provides a sessionId that shall be reused in all subsequent
exchanges as defined by the selected Protocol Binding.

The Handshake shall be performed using the HTTP REST Protocol Binding.

### Remote Management Session Step

During the Remote Management Session Step, the Remote Agent performs a
series of actions by sending Command Messages to the Local Agent.
Messages which are exchanges during this Remote Management Session Step
shall use the protocol binding which has been selected during the
Handshake Step.

Figure ‑ – Remote Agent processing during Remote Management Session

<img src="images/image8.png" style="width:6.16667in;height:3.96875in" />

As shown by the diagram, the first and last Message exchanged during the
Remote Management Session Step is respectively the RAM Start Command and
RAM Stop Command. Other RAM Commands are used to send APDUs to the
Secure Element, or Notification to the Device Application. Only the RAM
Start and RAM Stop Commands are mandatory.

The Local Agent processed each Command and if required send a Response
Message.

Protocol overview diagram
-------------------------

The following sequence diagram resume the main exchanges during a
Management Session. In the diagram the exchanges during the Remote
Management Session Steps are illustrated with the two types of RAM
Command that can be sent by a Remote Agent after the RAM Start Command
Message and until the RAM Stop Command Message.

Figure ‑ – Protocol overview

<img src="images/image9.png" style="width:6.69375in;height:6.12083in" />

Messages
========

The following type of Messages may be exchanges between Local Agent and
Remote Agent:

<span id="_Toc103330571" class="anchor"></span>Table ‑: Types of
Messages in GPSERAM

| Protocol Step     | Messages                 | From         | To           |
|-------------------|--------------------------|--------------|--------------|
| Handshake         | **Handshake Command**    | Local Agent  | Remote Agent |
| Handshake         | **Handshake Response**   | Remote Agent | Local Agent  |
| Remote Management | **RAM Start Command**    | Remote Agent | Local Agent  |
| Remote Management | **SE RAM Command**       | Remote Agent | Local Agent  |
| Remote Management | **SE RAM Response**      | Local Agent  | Remote Agent |
| Remote Management | **Notification Command** | Remote Agent | Local Agent  |
| Remote Management | **RAM Stop Command**     | Remote Agent | Local Agent  |

The next figure details the messages state machine:

Figure ‑ - Messages State Machine

<img src="images/image10.png" style="width:6.69375in;height:6.13681in" />

Below, we provide details for each message: its usage, its properties,
and the associated state machine transitions.

Handshake Command Message
-------------------------

### Message description

The Handshake Command Message is the Message used by the Local Agent
during the Handshake step as describes in section 2.3.2. This is the
first message exchanges between a Local Agent and a Remote Agent.

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330572" class="anchor"></span>Table ‑: Handshake
Command properties

| **Name**                    | **Description**                                                 | **No.** | **MOC** |
|-----------------------------|-----------------------------------------------------------------|---------|---------|
| sessionId                   | Identifier of the current Remote Management Session             | 1       | M       |
| secureElements              | A list of Secure Element available on Local Agent side          | 1       | M       |
| supportedTransportProtocols | A list of Transport Protocol supported by the Local Agent       | 1       | O       |
| supportedDataFormats        | A list of Data Format supported by the Local Agent.             | 1       | O       |
| supportedVersions           | A list of versions of GP SERAM RAM supported by the Local Agent | 1       | O       |

The items of secureElements property may be one of the following values:

-   eSE

-   uicc

-   euicc

The supportedTransportProtocols property may be one the following
values:

-   HTTPS

-   DTLS

The supportedDataFormats property may be one of the following values:

-   application/json

-   application/asn1

The supportedVersions property may be one of the following values:

-   1.0.1

-   2.0

Handshake Response Message
--------------------------

### Message description

The Handshake Response Message is the Message used by the Remote Agent
during the Handshake step as describes in section 2.3.2.

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330573" class="anchor"></span>Table ‑: Handshake
Response properties

| **Name**                | **Description**                                      | **No.** | **MOC** |
|-------------------------|------------------------------------------------------|---------|---------|
| sessionId               | Identifier of the current Remote Management Session  | 1       | M       |
| chosenSecureElement     | The Secure Element selected by the Remote Agent      | 1       | C       |
| chosenTransportProtocol | The Transport Protocol selected by the Remote Agent  | 1       | C       |
| chosenDataProtocol      | The Data Protocol selected by the Remote Agent.      | 1       | C       |
| chosenVersion           | The Version of GP SERAM selected by the Remote Agent | 1       | C       |

Each value of the chosen properties shall correspond of one supported by
the Local Agent.

If the Remote Agent capabilities doesn’t match those of Local Agent,
none of the chosen properties shall be set.

### Message State Machine

The following table lists the possible exchanges before and after this
one:

<span id="_Toc103330574" class="anchor"></span>Table ‑: Handshake
Response Message State Machine

| **Previous Message** | **Next Message**  |
|----------------------|-------------------|
| Handshake Command    | RAM Start Command |

RAM Start Command Message
-------------------------

### Message description

The RAM Start Command inform the Local Agent of the start of the Remote
Management Session by the Remote Agent.

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330575" class="anchor"></span>Table ‑: RAM Start
Command properties

| **Name**  | **Description**                                     | **No.** | **MOC** |
|-----------|-----------------------------------------------------|---------|---------|
| sessionId | Identifier of the current Remote Management Session | 1       | M       |

SE RAM Command Message
----------------------

### Message description

The SE RAM Command Message is used to send APDU to the Secure Element.

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330576" class="anchor"></span>Table ‑: SE RAM Command
properties

| **Name**    | **Description**                                                                                                                                                                  | **No.** | **MOC** |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|---------|
| sessionId   | Identifier of the current Remote Management Session                                                                                                                              | 1       | M       |
| stopOnError | Boolean value which determines whether the Local Agent shall continue sending the remaining APDU on Secure Element errors. If this property is not set, a true value is assumed. | 1       | O       |
| listOfCAPDU | An ordered list of APDU to send to the Secure Element. Each APDU are encoded as hexadecimal string.                                                                              | 1       | M       |

The Local Agent SHALL send each APDU command of the listOfCAPDU to the
Secure Element using the SE Access API. The Local Agent SHALL add each
APDU response to the SE RAM Response Message. If an APDU Response is not
available, the Local Agent SHALL use an empty string as APDU Response.
On any error to transmit an APDU command to the Secure Element, the
Local Agent SHALL discard all the remaining APDU commands and SHALL not
include any APDU response for the faulty transmission. Any warning or
error APDU response (i.e. 69xx or 68xx) are valid APDU response that
SHALL be inserted in the SE RAM Response. If the stopOnError flag of the
SE RAM Command is set to “true", the Local Agent SHALL not execute any
remaining APDU command after an error APDU response (i.e. 69xx).

SE RAM Response Message
-----------------------

### Message description

The SE RAM Response Message contains the APDU responses of the Secure
Element to a previous SE RAM Command Message.

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330577" class="anchor"></span>Table ‑: SE RAM Response
properties

| **Name**    | **Description**                                                                                      | **No.** | **MOC** |
|-------------|------------------------------------------------------------------------------------------------------|---------|---------|
| sessionId   | Identifier of the current Remote Management Session                                                  | 1       | M       |
| listOfRAPDU | An ordered list of APDU response of the Secure Element. Each APDU are encoded as hexadecimal string. | 1       | M       |

For each APDU send to the Secure Element, the Local Agent shall add the
associated APDU response to the listOfRAPDU property. If an APDU
response is not available (e.g. for a SELECT or on error), it shall be
filled with an empty string.

Notification Command Message
----------------------------

### Message description

The Notification Command Message is used to send processing notification
to the Device Application. The meaning of the notification and how it is
handled by the Device Application is implementation dependent

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330578" class="anchor"></span>Table ‑: Notification
Command properties

| **Name**              | **Description**                                          | **No.** | **MOC** |
|-----------------------|----------------------------------------------------------|---------|---------|
| sessionId             | Identifier of the current Remote Management Session      | 1       | M       |
| deviceAppNotification | An UTF-8 string to be notified to the Device Application | 1       | M       |

RAM Stop Command Message
------------------------

### Message description

A Remote Agent uses this Message to mark the end of its processing and
that no further Message will be sent.

Otherwise, the Local Agent shall notify the end of the Management
Session to the Device Application.

This Message marks the end of the processing by a Management Platform
and no further Message will be sent.

### Message Properties

The next table lists properties associated with this Message:

<span id="_Toc103330579" class="anchor"></span>Table ‑: RAM Stop Command
properties

| **Name**  | **Description**                                                                                                           | **No.** | **MOC** |
|-----------|---------------------------------------------------------------------------------------------------------------------------|---------|---------|
| sessionId | Identifier of the current Remote Management Session                                                                       | 1       | M       |
| statusMsg | An UTF-8 string which details the final status of the processing. If not provided an empty string value shall be assumed. | 1       | O       |

The Local Agent shall be sent the statusMsg to the Device Application.

HTTP REST Binding
=================

This part of the document details the HTTP REST binding which uses HTTPS
as Transport protocol layer and JSON as Data Format protocol layer.

The Local Agent acts as a HTTP client and the Remote Agent as a HTTP
Server. Messages defined in section 3.1 may be transmitted in exactly
one HTTP message or may be aggregated using a Message Pipelining during
the Remote Management Step as described below.

A formal API specification of the HTTP REST Binding is provided in Annex
A. This specification complies with the \[OAS\] and may be used to
generate code skeleton.

Management Session and HTTP messages
------------------------------------

HTTP messages are attached with a Management Session based on the
sessionId. The sessionId is explicitly inserted in each HTTP request and
implicitly determined for HTTP response based on the client-server
paradigm of the HTTP protocol.

Implementors MAY uses another HTTP mechanism to reinforce the HTTP
session (e.g. HTTP cookies). A Local Agent SHOULD support HTTP cookie as
defined in \[RFC6265\].

HTTP Polling
------------

To allow the Remote Agent to send its Command Messages to the Local
Agent, an HTTP polling is used as server-push mechanisms.

During HTTP polling, some void HTTP messages MAY be used. A void HTTP
message is an HTTP message which doesn’t carry any Command or Response
Message, and which role is to maintain the communication channel between
the Remote Agent and the Local Agent.

The Local Agent SHALL uses HTTP polling whenever awaiting a Command
Message. It SHALL NOT use HTTP Polling whenever it’s processing the
previous Command Message unless the Command Message doesn’t require a
Response Message.

The Remote Agent may fine-tune the HTTP polling by controlling the delay
between an HTTP response and the next HTTP request. If the Remote Agent
doesn’t specify any delay, the Local Agent SHALL make a pause of 1
second before sending the next HTTP request. The Remote Agent MAY used a
value of 0 to deactivate the delay.

As describes in \[RFC6202\], The Remote Agent MAY use HTTP long polling
mechanism and wait a certain period of time before sending the void HTTP
response. Remote Agent SOULD NOT used period of time beyond 30 seconds
and Local Agent SHOULD at least support latency of 30 seconds between
HTTP request and HTTP response.

Figure ‑ – HTTP polling

<img src="images/image11.png" style="width:4.80208in;height:4.59375in" />

Message Pipelining
------------------

The Message Pipelining allows multiple Message Commands to be sent over
a single HTTP response. An HTTP server MAY use Message Pipelining. An
HTTP client SHALL support Message Pipelining. It SHALL handle each
Message Commands one by one and the corresponding Message Responses, if
any, SHALL be included in the next HTTP polling request.

If a Local Agent detect an error during the processing of a Message
Command, it SHALL discard all the remaining Message Commands and only
consider Message Response until the Command Message that raises the
error.

The Message Pipelining is only available during the Remote Management
Step.

HTTP REST Endpoints
-------------------

Tow endpoints are defined: one to make the Handshakes step and the
second to makes the Remote Management step.

| /handshake        | Handshake step         |
|-------------------|------------------------|
| /remoteManagement | Remote Management Step |

Binding of the Handshake Step
-----------------------------

The Handshake Command SHALL be mapped to a HTTP POST request and the
associated Handshake Response to the associated HTTP Response.

Properties of the Handshake Command SHALL be bound according to Table
3‑20.

The body SHALL contains a JSON object with a "ramHandshakeCommand"
element.

<span id="_Ref97549956" class="anchor"></span>Table ‑– Handshake Command
binding

<table>
<thead>
<tr class="header">
<th><strong>Property Name</strong></th>
<th><strong>HTTP or JSON</strong></th>
<th><strong>Detail</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>sessionId</td>
<td>HTTP</td>
<td>A mandatory key/value query part of Handshake Endpoint. The key SHALL be "sessionId".</td>
</tr>
<tr class="even">
<td>secureElements</td>
<td>JSON</td>
<td>Mapped to a mandatory "secureElement" array element of "ramHandshake".</td>
</tr>
<tr class="odd">
<td>supportedTransportProtocols</td>
<td>JSON</td>
<td><p>Mapped to a mandatory "secureTransportProtocols" array element of "ramHandshake".</p>
<p>If not provided, ["HTTPS"] is assumed.</p></td>
</tr>
<tr class="even">
<td>supportedDataFormats</td>
<td>JSON</td>
<td><p>Mapped to a non-empty "supportedDataFormats" array element of "ramHandshake".</p>
<p>If not provided, ["application/json"] is assumed.</p></td>
</tr>
<tr class="odd">
<td>supportedVersions</td>
<td>JSON</td>
<td><p>Mapped to non-empty "supportedVersions" array element of ramHandshake.</p>
<p>If not provided, ["2.0.02"] is assumed.</p></td>
</tr>
</tbody>
</table>

If the HTTP Server could not fulfill the client proposal, it SHALL
respond with a 406 Not Acceptable response code, and the HTTP Server
SHOULD respond with a 404 Not Found if the sessionId is unknown on
server side. Otherwise, the HTTP server SHALL respond with a 200 Ok
response with an HTTP body that SHALL contains a JSON object with a
"ramHandshakeResponse" element.

Properties of the Handshake Response SHALL be bound according to Table
3‑21.

<span id="_Ref97551517" class="anchor"></span>Table ‑– Handshake Command
binding

| **Property Name**       | **level** | **Detail**                                                                                                            |
|-------------------------|-----------|-----------------------------------------------------------------------------------------------------------------------|
| sessionId               | HTTP      | Implicitly determined from the HTTP request                                                                           |
| chosenSecureElement     | JSON      | Mapped to a mandatory "chosenSecureElement" element of "ramHandshake" that contains the selected Secure Element.      |
| chosenTransportProtocol | JSON      | Mapped to a mandatory "chosenTransportProtocol" element of "ramHandshake" that contains the selected Transport layer. |
| chosenDataFormat        | JSON      | Mapped to a mandatory "chosenDataFormat" element of "ramHandshake" that contains the selected Data Format layer.      |
| chosenVersion           | JSON      | Mapped to a mandatory "chosenVersion" element of "ramHandshake" that contains the selected GP SERAM version.          |

Binding of the Remote Management
--------------------------------

### Binding of the HTTP request to the Remote Management Endpoint

During the Remote Management step, the HTTP client SHALL poll the
server. The poll request SHALL be an HTTP POST message that contains the
sessionId in a key/value format in the query part of the Remote
Management Endpoint, using "sessionId" as key.

Whenever some Message Responses shall be sent to the HTTP server, the
HTTP client SHALL add them to the polling request in a JSON body with a
"RemoteManagementMsg" element which is an JSON array. This item SHALL
NOT be empty. Items of the "RemoteManagementMsg" SHALL contains a
"ramResponseType" element with a value of "seRAMResponse".

### SE RAM Response Binding

The SE RAM Response Message SHALL be mapped an HTTP POST message.
Properties of the SE RAM Response Message SHALL be bound according to
Table 3‑22.

<span id="_Ref97565618" class="anchor"></span>Table ‑– SE RAM Response
binding

| **Property Name** | **level** | **Detail**                                                                                                                                                                  |
|-------------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| sessionId         | HTTP      | A mandatory key/value query part of Remote Management Endpoint. The key SHALL be "sessionId".                                                                               |
| listOfRAPDU       | JSON      | A mandatory element of "RemoteManagementMsg" mapped to a "listofRAPDU" element that is a JSON array of APDU response. Each APDU response are encoded as hexadecimal string. |

### Binding of the HTTP Response for Remote Management Endpoint

On any error detected in the JSON properties of the HTTP request, the
Remote Agent SHALL immediately ends the Management Session by sending
the RAM Stop Command Message. The Remote Agent SHOULD respond with an
HTTP 404 Not Found if the sessionId is unknown on server side.

Otherwise, the Remote Agent SHALL respond with an HTTP 200 Ok response.
The HTTP response MAY include a JSON object as HTTP body with a
"remoteManagementOrder" element which is a JSON object. The
"remoteManagementOrder" object MAY contains a "delay" element or a
"ramCommand" element, or both. The "delay" element is an integer that
represent the number of second the Local Agent had to wait for the
polling request, as described in section 3.2.2. The "ramCommand" element
is a JSON array which items SHALL contains a "ramCommandType" element
with a value of "ramStartCommand" or "seRamCommand" or
"notificationCommand", or "ramStopCommand" according with the type of
Message Command.

If the "delay" element is not present in the HTTP response, the Local
Agent SHALL assume a one second default value for the HTTP polling.

The "ramCommand" SHALL contain at least one Command Message. If the
"ramCommand" contains more than one Command Message, the Local Agent
SHALL handles them as detailed in section 4.3.

### RAM Start Command binding

To send a RAM Start Command, the Remote Agent SHALL add an object in the
"ramCommand" object with a "ramStartCommand" value in the
"ramCommandType" element. The properties of the RAM Start Command SHALL
be bound according to Table 3‑24.

<span id="_Ref97579795" class="anchor"></span>Table ‑– Handshake Command
binding

| **Property Name** | **HTTP or JSON** | **Detail**                                  |
|-------------------|------------------|---------------------------------------------|
| sessionId         | HTTP             | Implicitly determined from the HTTP request |

### SE RAM Command binding

To send a SE RAM Command, the Remote Agent SHALL add an object in the
"ramCommand" object with a "seRamCommand" value in the "ramCommandType"
element. The properties of the SE RAM Command SHALL be bound according
to Table 3‑25.

<span id="_Toc103330584" class="anchor"></span>Table ‑– Handshake
Command binding

| **Property Name** | **HTTP or JSON** | **Detail**                                                                                                                                    |
|-------------------|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| sessionId         | HTTP             | Implicitly determined from the HTTP request                                                                                                   |
| stopOnError       | JSON             | Mapped to a "stopOnError" element of a "ramCommand" item that is a boolean value with a default "true" value.                                 |
| listOfCAPDU       | JSON             | Mapped to a "listOfCAPDU" element of a "ramCommand" item that is an array of UTF-8 string that represent APDU command encoded in hexadecimal. |

Any error to transmit a APDU command to the secure Element, or if the
Secure Element respond with a 69xx APDU response with a stopOnError flag
set to "true", the Local Agent SHALL discard all the remaining Command
Message in the Message Pipeline.

Transmission Errors
-------------------

The Local Agent and the Remote Agent relies on HTTP protocol to their
exchanges. Most of the transmission errors will be transparently resumed
by the TCP (Transmission Control Protocol) end to end stacks.

Nevertheless, some errors are unrecoverable at TCP level. This section
lists the rules Local Agent and Remote Agent shall follow to enforce the
Message delivery or error detection.

Errors may result in a failure on each level of the exchanges:

-   the Local Agent

-   the Remote Agent

-   the Device

-   the Remote Management Platform

-   the network

If the HTTPS connection is prematurely closed, the local Agent SHOULD
immediately try to re-establish the HTTPS connection with the Remote
Agent.

If the Local Agent could not establish an HTTPS connection or send an
HTTP request to the Remote Agent, it SHOULD retry during 5 minutes at
the most.

If the Local Agent doesn’t receive an HTTP response within a 40 second
delay from the Remote Agent, it SHOULD close the current HTTPS
connection and try to re-establish a new one.

If a Remote Agent could not send an HTTP response to a Local Agent
within a 30 second delay, it SHALL close the current HTTPS connection
and SHOULD awaiting the Local Agent reconnection.

A Remote Agent SHOULD consider having lost a Local Agent after a
5-minute delay.

TBC

####### HTTP Rest Binding API Specification

The OpenAPI specification of the HTTP Rest Binding is available at the
following URI: https://api.globalplatform.org/gpseram/v2.

openapi: "3.0.3"

info:

title: "GP SERAM"

version: "2.0.0"

description: "This specification allows a Remote Agent to manage a
Secure\\

\\ Element thanks to a Local Agent.

\\ \<br\> \<br\> 2 layers is provided here:\<br\>\\

\\ - the data model which details messages which are exchanges. Refer to
the Schemas\\

\\ section below.\<br\> - the HTTP REST binding, details in terms of
HTTP GET and\\

\\ POST methods. Refer to initialization and seApplicationManagement
phase below.\<br\> \<br\> The\\

\\ Remote Agent is acting as an HTTP server and the Local Agent as\\

\\ an HTTP Client. During the initialization phase, the local Agent
provides a list of\\

\\ supported data format and transport protocol. The server selects the
best option \<br\>"

tags:

\- name: "seApplicationManagementService"

description: "Remote Agent performs the remote management by sending
orders\\

\\ to the Local Agent"

paths:

/handshake:

post:

tags:

\- "seApplicationManagementService"

summary: "method used to negociate data format and the transport
protocol"

description: "This method shall be used by the HTTP client to tell which
data\\

\\ formats and which transport protocols it supports. In response, the
HTTP\\

\\ server shall tell which ones it selects.\<br\> \<br\> The DataFormat
describes\\

\\ which data format is used for message payloads.\<br\> \<br\> The
TransportProtocol\\

\\ describes the transport protocol binding. It details how setting-up
the\\

\\ transport layer (if required), exchanging the messages payload
between RA\\

\\ and LA, keeping the integrity of the remote management session and
resuming\\

\\ communications in case of a data exchange failure\<br\> \<br\> The
HTTP client shall\\

\\ used the sessionId to allow the HTTP server to track the remote
management. The\\

\\ HTTP client shall reused the sessionId in all subsequent HTTP request
to the\\

\\ HTTP server"

parameters:

\- name: sessionId

in: query

required: true

schema:

\$ref: "\#/components/schemas/sessionId"

requestBody:

content:

application/json:

schema:

required:

\- "ramHandshakeCommand"

properties:

ramHandshakeCommand:

\$ref: "\#/components/schemas/ramHandshakeCommand"

responses:

"200":

description: "OK"

content:

application/json:

schema:

required:

\- "remoteManagementHandshakeResponse"

properties:

remoteManagementHandshakeResponse:

\$ref: "\#/components/schemas/ramHandshakeResponse"

"404":

description: Session id not found

"406":

description: The server could not fulfill the client proposal

/remoteManagement:

post:

tags:

\- "seApplicationManagementService"

summary: "Method used to respond to a remote management command."

description: "This method shall be used by the HTTP client to transmit
its response\\

\\ to the HTTP server. \<br\>\<br\> In response, the HTTP server shall
send the next remote\\

\\ management order."

parameters:

\- name: sessionId

in: query

required: true

schema:

\$ref: "\#/components/schemas/sessionId"

requestBody:

content:

application/json:

schema:

properties:

remoteManagementMsg:

\$ref: "\#/components/schemas/remoteManagementMsg"

responses:

"200":

description: "OK"

content:

application/json:

schema:

required:

\- "remoteManagementOrder"

properties:

remoteManagementOrder:

\$ref: "\#/components/schemas/remoteManagementOrder"

"404":

description: Session id not found

components:

schemas:

apdu:

description: "hexadecimal encoded APDU"

type: "string"

pattern: "(\[0-9A-F\]{2})\*"

dataFormat:

type: "string"

enum:

\- "application/json"

\- "application/asn1"

secureElement:

type: "object"

properties:

seType:

type: "string"

enum:

\- "eSE"

\- "uicc"

\- "euicc"

seId:

type: "string"

example: "89001012012341234012345678901224"

sessionId:

type: "string"

format: "uuid"

example: "0f96af97-f879-45a2-a011-ee7439df3804"

transportProtocol:

type: "string"

enum:

\- "HTTPS"

\- "DTLS"

version:

type: "string"

enum:

\- "1.0.1"

\- "2.0.0"

ramCommand:

required:

\- ramCommandType

discriminator:

propertyName: "ramCommandType"

properties:

ramCommandType:

type: string

enum:

\- "ramStartCommand"

\- "seRamCommand"

\- "notificationCommand"

\- "ramStopCommand"

ramStartCommand:

allOf:

\- \$ref: '\#/components/schemas/ramCommand'

ramStopCommand:

allOf:

\- \$ref: '\#/components/schemas/ramCommand'

properties:

statusMsg:

description: "A string which details the final status of the processing"

type: "string"

default: ""

notificationCommand:

allOf:

\- \$ref: '\#/components/schemas/ramCommand'

\- required:

\- "deviceAppNotification"

properties:

deviceAppNotification:

description: "notification string to send to the device Application"

type: "string"

example: "step1/action2/OK"

seRamCommand:

allOf:

\- \$ref: '\#/components/schemas/ramCommand'

\- required:

\- "listOfCAPDU"

properties:

stopOnError:

type: "boolean"

default: "true"

listOfCAPDU:

type: "array"

items:

\$ref: "\#/components/schemas/apdu"

example:

\- "00A4040008A00000015100000000"

ramResponse:

required:

\- ramResponseType

discriminator:

propertyName: "ramResponseType"

properties:

ramResponseType:

type: string

enum:

\- "seRamResponse"

seRamResponse:

allOf:

\- \$ref: '\#/components/schemas/ramResponse'

\- required:

\- "listOfRAPDU"

properties:

listOfRAPDU:

type: "array"

items:

\$ref: "\#/components/schemas/apdu"

example:

\- "9000"

remoteManagementOrder:

description: "This is the data model for the remote management command."

properties:

delay:

description: "Delay in second the device local agent had to wait
before\\

\\ sending data to the platform."

type: "integer"

default: 1

ramCommands:

type: array

items:

\$ref: "\#/components/schemas/ramCommand"

remoteManagementMsg:

description: "This is the data model for the remote management
containing SE ram responses. A polling message shall be empty"

type: "object"

properties:

ramResponses:

description: "list of RAM responses"

type: "array"

minItems: 1

items:

\$ref: "\#/components/schemas/ramResponse"

ramHandshakeCommand:

description: "This is the data model for the handshake command.
\<br\>\<br\> If supportedTransportProtocol\\

\\ is not provided, HTTPS is assumed. If supportedDataFormat is not
provided,\\

\\ application/json is assumed. \<br\>If supportedVersions is not
provided, 2.0.0 is assumed"

type: "object"

required:

\- "seIdType"

\- "seID"

properties:

secureElements:

type: "array"

items:

\$ref: "\#/components/schemas/secureElement"

supportedTransportProtocols:

type: "array"

items:

\$ref: "\#/components/schemas/transportProtocol"

supportedDataFormats:

type: "array"

items:

\$ref: "\#/components/schemas/dataFormat"

supportedVersions:

type: "array"

items:

\$ref: "\#/components/schemas/version"

ramHandshakeResponse:

description: "This is the data model for the handshare response. All
fields are mandatory"

type: "object"

required:

\- "chosenSecureElement"

\- "chosenDataProtocol"

\- "chosenTransportProtocol"

\- "chosenVersion"

properties:

chosenSecureElement:

\$ref: "\#/components/schemas/secureElement"

chosenTransportProtocol:

\$ref: "\#/components/schemas/transportProtocol"

chosenDataFormat:

\$ref: "\#/components/schemas/dataFormat"

chosenVersion:

\$ref: "\#/components/schemas/version"

####### SAM Use Case

TBC

####### LPA Proxy Use Case

TBC
