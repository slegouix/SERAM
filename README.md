GP SERAM v2
===========

Introduction
------------

The aim of this GP SERAM v2 is to define an administration protocol to 
perform remote management of applications residing on any type of Secure
Element in a device (e.g. mobile phone, tablet...). Remote management
means the delivery of management commands to a Secure Element hosted in
a device and the management of the related responses. These management
commands could for instance be used to install an application and to
provide data to an application. Examples of applications could be
payment applications, transit applications, or access applications.

To understand the protocol, please, refer to:

* [GP SERAM Protocol Overview](doc/GPSERAM_v2__Overview.md)
* [GP SERAM Messages](doc/GPSERAM_v2__Messages.md)

Protocol binding
----------------

For **GP SERAM - HTTP REST Binding** please refer to:

* [HTTP REST Binding - OpenAPI specification](spec/gpseram_v2.yaml) ([Viewer](https://slegouix.github.io/SERAM/))
* [HTTP REST Binding - explanation](doc/GPSERAM_v2_HTTP_REST_Binding.md)

For **GP SERAM - COAP Binding** please refer to:
* [CoAP Binding - CDDL specification](spec/gpseram_v2.ccdl)
* [CoAP Binding - explanation](doc/GPSERAM_v2__CoAP_Binding.md)

Use Case
--------

* [SAM Use Case](GPSERAM_v2_SAM_Use_Case.md)
* [LPA Proxy Use Case](GPSERAM_v2_LPA_Proxy_Use_Case.md)

