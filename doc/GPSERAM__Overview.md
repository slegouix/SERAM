GP SERAM - Protocol Overview
============================

This specification defined a protocol that allows a [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) to request the management of a [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement) by a [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform).

The following figure present the generic architecture of GP SERAM and its environment. All part in blue is defined in this specification.

![Architecture Overview](images/GP_SERAM__Architecture_Overview.png)

In this architecture, a [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and a [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) manage the protocol to allow the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform) to interact with a [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) and the [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement).

While [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform), [Device](GPSERAM__TerminologyAndDefinitions.md#Device) and [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement) are physical entities, the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) and [Core Management Platform](GPSERAM__TerminologyAndDefinitions.md#CoreManagementPlatform) are logical components, and the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) in this specification are to be considered as functions. These functions might be implemented as standalone applications or as libraries integrated inside larger business applications. In the GP SERAM context, the terms [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) may be used interchangeably with *Remote Agent function* and *Local Agent function*.

Note that if the [Device](GPSERAM__TerminologyAndDefinitions.md#Device) embeds a [Trusted Execution Environment](GPSERAM__TerminologyAndDefinitions.md#TrustedExecutionEnvironment), the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) may be implemented in this [Trusted Execution Environment](GPSERAM__TerminologyAndDefinitions.md#TrustedExecutionEnvironment).  Otherwise, the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) is implemented in the [Rich Execution Environment](GPSERAM__TerminologyAndDefinitions.md#RichExecutionEnvironment) of the [Device](GPSERAM__TerminologyAndDefinitions.md#Device). In these two cases, the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) will have to use an API providing access to the [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement) and allowing it to forward commands to an application in this [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement). This API is called an [SE Access API](GPSERAM__TerminologyAndDefinitions.md#SEAccessAPI) in this specification. The [SE Access API](GPSERAM__TerminologyAndDefinitions.md#SEAccessAPI) can be the [SIMalliance GlobalPlatform Open Mobile API](https://globalplatform.org/specs-library/open-mobile-api-specification-v3-3/) when the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) is implemented in the [Rich Execution Environment](GPSERAM__TerminologyAndDefinitions.md#RichExecutionEnvironment) (REE), and the [GlobalPlatform TEE Secure Element API](https://globalplatform.org/specs-library/tee-secure-element-api/) when the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) is implemented in the [Trusted Execution Environment](GPSERAM__TerminologyAndDefinitions.md#TrustedExecutionEnvironment) (TEE).

More precisely, an [SE Access API](GPSERAM__TerminologyAndDefinitions.md#SEAccessAPI) usually provides mechanisms for a device application to open a connection with the [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement) and then to open a logical channel with a card application in order to send *APDUs* to this application.

Protocol design
---------------

### Protocol Layers

GP SERAM is a message-oriented protocol which used the following protocol stack:

![Protocol layers](images/GP_SERAM__Protocol_layers.png)

[Messages](GPSERAM__TerminologyAndDefinitions.md#Message) are the data exchanged between the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent). The definition, the meaning and sequency of [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) are states below in the [Messages section](#messages).

How [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) are carried on the network relies on the [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding) used by the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent). A [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding) defines the rules to map [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) on the [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) layer and the [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) layer.

This version of the specification defined the following [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) and [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocol layers:

-   **HTTPS** as [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport)

-   **JSON** as [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat)

Others protocol layer elements may be defined in the future.

This version of the specification defined the following [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding):

-   **HTTP REST**, which uses HTTPS as [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocol and JSON as [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat).

Others [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding) may be defined in the future.

### Management Session

To synchronize the management process, a [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) and a [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform) relies on a session identifier, named [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId). The [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) is shared and used for all communications between the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) and the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent).

The timing frame during which it is performed some management tasks that are associated with one sessionId is called the [Management Session](GPSERAM__TerminologyAndDefinitions.md#ManagementSession).

![Management Session](images/GP_SERAM__Management_Session.png)

The [Management Session](GPSERAM__TerminologyAndDefinitions.md#ManagementSession) is started by the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) and is then controlled until its end by the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent).

### Protocol Steps

As shown by the next figure, the protocol has three steps. First the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) is triggered by the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) with the [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) and the [RA Endpoint](GPSERAM__TerminologyAndDefinitions.md#RAEndpoint). It then negotiates the [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) and [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocol to use with the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent). Finally, the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) make the task required for the [Management Session](GPSERAM__TerminologyAndDefinitions.md#ManagementSession).

![Protocol Steps](images/GP_SERAM__Protocol_Steps.png)

Most of the [Initialization](GPSERAM__TerminologyAndDefinitions.md#Initialization) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) is out of the scope of this document.

During the [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step), the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) perform a protocol negotiation with the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent), by sending its capabilities, the session identifier, and a list of manageable [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement). A [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding) to be used for the next step is then selected.

Next, the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform) starts a [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) by sending a *Start* [Command](GPSERAM__TerminologyAndDefinitions.md#Command). During the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange), the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform) may send *APDUs* to the [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement), or send notifications to the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication). The end of the [Management Session](GPSERAM__TerminologyAndDefinitions.md#ManagementSession) is announced by a *Stop* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) form the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform).

Protocol Steps details
----------------------

### Initialization Step

During this phase, a session identifier, named [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId), SHALL be generated and shared between the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) and the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform). How this [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) is generated and shared is out of the scope of this specification.

The [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) SHALL be a unique identifier for the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication) and the [Management Platform](GPSERAM__TerminologyAndDefinitions.md#ManagementPlatform). How this uniqueness is handled is out of the scope of this specification.

Moreover, the [RA Endpoint](GPSERAM__TerminologyAndDefinitions.md#RAEndpoint) to communicate with the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) MUST also has been shared between entities. How this [RA Endpoint](GPSERAM__TerminologyAndDefinitions.md#RAEndpoint) is defined and shared is out of the scope of this specification.

GP SERAM session starts after the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) is triggered with the [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) and the [RA Endpoint](GPSERAM__TerminologyAndDefinitions.md#RAEndpoint) of the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent). The [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) starts with the [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step).

### Handshake Step

As soon as it was triggered, the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) shall start the [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step).

Handshaking allows a [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) and [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) to negotiate the [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) and the [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocol used to perform the remote management.

This method shall be used by the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) to tell which [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) and which [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocols it supports. In response, the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) shall tell which ones it selects.

The attributes which are negotiate are:

-   The secure elements: the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) provides a list of [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement) which may be targeted by the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent)

-   The [Data Format](GPSERAM__TerminologyAndDefinitions.md#DataFormat) (e.g. JSON)

-   The [Transport](GPSERAM__TerminologyAndDefinitions.md#Transport) protocols (e.g. HTTPS)

-   The version of the protocol

To allow the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) to track the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) provides a [sessionId](GPSERAM__TerminologyAndDefinitions.md#sessionId) that shall be reused in all subsequent exchanges as defined by the selected [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding).

The [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) shall be performed using the *HTTP REST* [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding).

### Command Exchange Step

During the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step), the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) performs a series of actions by sending [Commands](GPSERAM__TerminologyAndDefinitions.md#Command) to the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent). The [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) processed each [Command](GPSERAM__TerminologyAndDefinitions.md#Command) and if required send a [Response](GPSERAM__TerminologyAndDefinitions.md#Response) to the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent).

[Commands](GPSERAM__TerminologyAndDefinitions.md#Command) and [Responses](GPSERAM__TerminologyAndDefinitions.md#Response) are exchanged using, respectively, *Order* [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) and *Report* [Messages](GPSERAM__TerminologyAndDefinitions.md#Message). An  *Order* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) sent by the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) may carry one or more [Command](GPSERAM__TerminologyAndDefinitions.md#Command). Similary, a *Report* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) from the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) may carry one or more [Response](GPSERAM__TerminologyAndDefinitions.md#Response). [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) which are exchanges during the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) shall use the [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding) which has been selected during the [Handshake](GPSERAM__TerminologyAndDefinitions.md#Handshake) [Step](GPSERAM__TerminologyAndDefinitions.md#Step).

![Remote Agent processing during Command Exchange](images/GP_SERAM__Remote_Agent_processing_during_Command_Exchange.png)

As shown by the diagram, the first and last [Command](GPSERAM__TerminologyAndDefinitions.md#Command) of the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) is respectively the *Start* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) and *Stop* [Command](GPSERAM__TerminologyAndDefinitions.md#Command). Other *RAM* [Commands](GPSERAM__TerminologyAndDefinitions.md#Command) are used to send *APDUs* to the [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement), or *Notifications* to the [Device Application](GPSERAM__TerminologyAndDefinitions.md#DeviceApplication). Only the *Start* and *Stop* [Commands](GPSERAM__TerminologyAndDefinitions.md#Command) are mandatory.

Protocol overview diagram
-------------------------

The following sequence diagram resumes the main exchanges during a [Management Session](GPSERAM__TerminologyAndDefinitions.md#ManagementSession). In the diagram the exchanges during the [Command Exchange](GPSERAM__TerminologyAndDefinitions.md#CommandExchange) [Step](GPSERAM__TerminologyAndDefinitions.md#Step) are illustrated with the two types of *RAM* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) that can be sent by a [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) after the *Start* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) and until the *Stop* [Command](GPSERAM__TerminologyAndDefinitions.md#Command).

![Protocol overview](images/GP_SERAM__Protocol_overview.png)

Messages
========

The following type of [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) may be exchanged between [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) and [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent):

| Protocol Step     | Messages               | From         | To           |
|-------------------|------------------------|--------------|--------------|
| Handshake         | **Handshake Command**  | Local Agent  | Remote Agent |
| Handshake         | **Handshake Response** | Remote Agent | Local Agent  |
| Remote Management | **Order**              | Remote Agent | Local Agent  |
| Remote Management | **Report**             | Local Agent  | Remote Agent |

Local Agent Behaviour
=====================

As soon as triggered, the Local Agent starts the Handshake Step by sending a *Handshake Command* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) to the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent) with the a list of manageable [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement).

On reception of the *Handshake Response* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) from the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent), the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) goes to the next *Command Exchange* [Step](GPSERAM__TerminologyAndDefinitions.md#Step) using the selected [Protocol Binding](GPSERAM__TerminologyAndDefinitions.md#ProtocolBinding). During this [Step](GPSERAM__TerminologyAndDefinitions.md#Step), the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) proceed the [Command](GPSERAM__TerminologyAndDefinitions.md#Command) from *Order* [Messages](GPSERAM__TerminologyAndDefinitions.md#Message) one-by-one and send, if required, send back a *Report* [Message](GPSERAM__TerminologyAndDefinitions.md#Message) with the [Responses](GPSERAM__TerminologyAndDefinitions.md#Response). As the first [Command](GPSERAM__TerminologyAndDefinitions.md#Command) shall be a *Start* and the last one a *Stop*, the internal state machine of the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) is detailed in the next figure:

![Messages State Machine](images/GP_SERAM__Messages_State_Machine.png)

On *Notification* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) shall sent a notification to the [Device Application](GPSERAM__TerminologyAndDefinitions.md#Device Application). How the [Device](GPSERAM__TerminologyAndDefinitions.md#Device) notify the [Device Application](GPSERAM__TerminologyAndDefinitions.md#Device Application) and its reliability is implementation dependent. *Notification* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) do not require *Response*, nor a state change.

On *SE RAM* [Command](GPSERAM__TerminologyAndDefinitions.md#Command) the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) shall sent each *C-APDU* to the selected [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement) accordling with the [SE Access API](GPSERAM__TerminologyAndDefinitions.md#SEAccessAPI) and add the *R-APDU* to the *SE RAM* *Response*. If the *C-APDU* is a *SELECT Command* as defined by [GP Card Specification](https://globalplatform.org/specs-library/card-specification-v2-3-1/), the [SE Access API](GPSERAM__TerminologyAndDefinitions.md#SEAccessAPI), may required the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) to use a dedicated function to open a *logicial channel* with the [Secure Element](GPSERAM__TerminologyAndDefinitions.md#SecureElement). This *logicial channel* shall be used for the subsequent *C-APDU* and closed if another *SELECT Command* is received or at the end of the *Management Session*. On any error to transmit the *C-APDU*, the [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) shall discard all the remaining "C-APDU" and shall not include any *R-APDU* in the response for the faulty transmission. The [Local Agent](GPSERAM__TerminologyAndDefinitions.md#LocalAgent) do not need to parse and handle *R-APDU*. All *R-APDU* shall be transmit to the [Remote Agent](GPSERAM__TerminologyAndDefinitions.md#RemoteAgent), including those with a 69xx or 68xx status word.

