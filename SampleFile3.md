**Contents**

- [Introduction](#introduction)
- [Purpose of OXPd](#purpose-of-oxpd)
- [Overview of OXPd 1.6 General Architecture](#overview-of-oxpd-1-6-general-architecture)
    - [OXPd Programming Model Differences](#oxpd-programming-model-differences)
    - [OXPd 1.6 system components](#oxpd-1-6-system-components)
    - [Web UI client (browser application)](#web-ui-client-browser-application)
    - [Device Web API](#device-web-api)
    - [Decouple device UI constructs from device web API](#decouple-device-ui-constructs-from-device-web-api)
    - [Clients and services](#clients-and-services)
    - [Decouple data, service, and binding models](#decouple-data-service-and-binding-models)
    - [Backwards Compatibility](#backwards-compatibility)
- [Web browser Configuration and Interaction](#web-browser-configuration-and-interaction)
    - [OXPd UIConfiguration](#oxpd-uiconfiguration)
    - [`BrowserApp` invocation](#browserapp-invocation)
    - [Native Dialogs](#native-dialogs)
- [Sessions and Context Management](#sessions-and-context-management)
    - [Sessions and Stateless Web Service Connections](#sessions-and-stateless-web-service-connections)
    - [Device Sessions](#device-sessions)
    - [OXPd Context Model](#oxpd-context-model)
        - [Device Context IDs](#device-context-ids)
            - [UI Context IDs](#ui-context-ids)
            - [Accessory Context IDs](#accessory-context-ids)
        - [Server IDs](#server-ids)
            - [Server Session IDs](#server-session-ids)
            - [Server Context IDs](#server-context-ids)
        - [High-level System Sequence with Context IDs](#high-level-system-sequence-with-context-ids)
- [Binding Model](#binding-model)
- [Device Services](#device-services)
    - [`Test` Service](#test-service)
    - [`CertificateManagement` Service](#certificatemanagement-service)
    - [`UIConfiguration` Service](#uiconfiguration-service)
    - [`Scan` Service](#scan-service)
    - [`Accessories` Service](#accessories-service)
    - [`DeviceInfo` Service](#deviceinfo-service)
    - [`Authentication` Service](#authentication-service)
    - [`Authorization` service](#authorization-service)
    - [`Statistics` Service](#statistics-service)
    - [`Quota` Service](#quota-service)
    - [`Copy` Service](#copy-service)
- [Quality of Service Best Practices](#quality-of-service-best-practices)
- [Target Platforms](#target-platforms)


# Introduction
<div id="introduction"></div>

This document provides a high-level overview of the overall approach and architecture for OXPd 1.6.

This architectural overview is considered a top level document. Additional documents describe other aspects of OXPd 1.6 and other OXPd modules in more detail. These documents are intended as overviews and usage guides, and are accompanied by additional reference specifications that provide API details.

# Purpose of OXPd
<div id="purpose-of-oxpd"></div>

OXPd is part of HP’s Open eXtensibility Platform (OXP) and represents the device layer of this platform. OXPd is intended as a fleet-wide SDK and specification that operates in a uniform way across a wide variety of devices. The idea of OXPd is to enable web application based solutions that can be implemented once, and then execute across the fleet. This saves time and energy associated with the development, qualification, deployment, and updates of solutions.

The focus of OXPd, both historically and as of this version, is still primarily to enable document workflow applications and middleware that support scanning, printing, document manipulation, and interaction with back-end systems.

The following diagram conceptually depicts a typical OXPd system when deployed as a document workflow middleware server.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-OXPdOverview.png" alt="OXPdArchitecturalOverviewSpec-OXPdOverview" width="80%">
<br>*OXPd Overview*
<br>

# Overview of OXPd 1.6 General Architecture
<div id="overview-of-oxpd-1-6-general-architecture"></div>

This version of OXPd introduces a very different model as compared to prior versions of OXPd. This break in the model is necessary to support the enhanced capabilities and flexibility offered by 1.6.

## OXPd Programming Model Differences
<div id="oxpd-programming-model-differences"></div>

Prior versions of OXPd utilize a single XML configuration schema that drives user interface definitions.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-Prior1_4Schema.png" alt="OXPdArchitecturalOverviewSpec-Prior1_4Schema" width="100%">
<br>*OXPd 1.4 Model*
<br>

This approach is simple, but does not provide enough flexibility for UI layout, interactive business logic, etc.

The OXPd 1.6+ model supports a true web application model that allows interactive business logic and flexible UI layout at the MFP device’s control panel, using standard web application development tools.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-1_6Schema.png" alt="OXPdArchitecturalOverviewSpec-1_6Schema" width="100%">
<br>*OXPd 1.6 Model*
<br>

## OXPd 1.6 system components
<div id="oxpd-1-6-system-components"></div>

The following diagram depicts a high-level view of the components involved in an externally deployed solution based on OXPd 1.6 technology.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-SystemComponents.png" alt="OXPdArchitecturalOverviewSpec-SystemComponents" width="100%">
<br>*OXPd 1.6 System Components*
<br>
Color legend:

- Dark blue components represent embedded firmware.
- Yellow components represent adapters that implement the OXPd service definitions.
- Green components inside the device represent the binding layer.
- Red components represent user credentials for accessing device features and/or other
network resources.
- Orange components represent a web application.
- Black components represent the existing OXPd 1.4.x workflow implementation.

## Web UI Client (browser application)
<div id="web-ui-client-browser-application"></div>

The introduction of web-browser technology in the device significantly improves the ability for web applications and solutions to provide rich UI experiences at the MFP device while reducing device-specific code. The use of standard AJAX patterns that utilize JavaScript and HTML allows a rich UI to be developed using standard, modern web application tools.

## Device Web API
<div id="device-web-api"></div>

Where prior versions of OXPd have primarily supported only scanning workflows, exposed via XML configuration files, OXPd 1.6 is adding new service endpoints. The new web API follows a more traditional API model, with separate services that support service-related methods and data types. These services provide individual methods that each provide well defined, specific behaviors and responses from the device. This approach makes development and testing easier and more natural. More details are specified in related documentation describing each of the device web services.

## Decouple Device UI Constructs from Device Web API
<div id="decouple-device-ui-constructs-from-device-web-api"></div>

Prior to version 1.6, OXPd has supported UI constructs and scan action tickets being combined into the same XML payload. This has not been a significant issue since the UI constructs have not been standards-based. With the introduction of standards-based browser technology, OXPd 1.6 supports an architectural model where web UI constructs (HTML/JavaScript) are decoupled from the rest of the web APIs on the device.

## Clients and Services
<div id="clients-and-services"></div> This logical stack also illustrates that, in terms of the user interface, the device acts a client and the web application is a server. For printing and imaging services, the web application is a client to the device’s services.

The following diagram depicts both physical and logical views of these concepts.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-ClientAndServiceOverview.png" alt="OXPdArchitecturalOverviewSpec-ClientAndServiceOverview" width="80%">
<br>*Client and Service Overview*
<br>

## Decouple Data, Service, and Binding Models
<div id="decouple-data-service-and-binding-models"></div>

Prior versions of OXPd have been based on simple XML document exchanges over HTTP, but have stopped short of naming a binding protocol such as REST or SOAP. As of version 1.6, OXPd is recognizing the following independent aspects of the device’s web API:

1. The data model, which includes the schema for the objects (also referred to as the “nouns”) being passed between the device and the web application. This includes
things like scan tickets, print tickets, security contexts, etc.
2. The service model, which includes the service methods or operations (also referred to as the “verbs”) that are supported on the device or the web application. This includes the behavioral aspects of what is expected in the way of return values, error
handling, etc.
3. The binding model describes how the data model and the service model are exposed and bound to. This includes things like REST patterns, SOAP, WSDL description exposure, native bindings from within a device, service discovery, use of WS protocols, etc.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-DataServiceBindingModels.png" alt="OXPdArchitecturalOverviewSpec-DataServiceBindingModels" width="50%">
<br>*Data, Service, and Binding Models Overview*
<br>

**Note:** Core functionality is maintained across all bindings, even if a
particular binding changes the form of the services.

## Backwards Compatibility
<div id="backwards-compatibility"></div>

Although OXPd1.6 represents a change in architecture, flexibility and capability, existing solutions rely on compatibility with prior versions of OXPd (OXPd:Workflow1.4). HP devices that support OXPd support both the older OXPd:Workflow1.4 simple XML and the new OXPd1.6 browser/web-services models, allowing both existing and next generation OXPd solutions to run against the same device. HP devices achieve backward compatibility by supporting both OXPd models side by side.

# Web browser Configuration and Interaction
<div id="web-browser-configuration-and-interaction"></div>

This section provides an overview of OXPd configuration and the interaction model for the web browser. More detailed specifications for the service model operations and data model objects are provided in subsequent sections.

## OXPd UIConfiguration
<div id="oxpd-uiconfiguration"></div>

In previous versions of OXPd, the term configuration file has typically referred to documents with content ranging from partial fragments to comprehensive configurations, including home screen buttons, UI constructs, and tickets. As of this version of OXPd, configuration files are now separate entities, distinct from UI content (HTML/JavaScript) or data model payloads that are passed to/from web API service endpoints.

As such, configuration files in OXPd 1.6 only include the configurable aspects of the OXPd UI. The configuration schema itself, along with a more detailed description of the configurable elements, is described below in the appropriate data model and service model sections. This section continues to discuss the overall configuration model.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-OXPdUIConfiguration.png" alt="OXPdArchitecturalOverviewSpec-OXPdUIConfiguration" width="80%">
<br>*OXPd UIConfiguration Overview*
<br>

There are still OXPd configurations, but they are more focused now on user interface and browser settings and topLevel home screen buttons configurations.

## `BrowserApp` Invocation
<div id="browserapp-invocation"></div>

Whenever the browser application is invoked, the specified URL is first compared with the browser’s configured trusted sites. If the URL falls within the approved sites, the request is allowed, otherwise an error message displays.

After these security checks pass, the device’s browser is invoked and the browser requests the specified URL from the web application. At this point, the browser and the web application are in control of the control panel’s user interface through the exchange of HTML/JavaScript between the browser client and the web application server.

The browser is actually contained within an application (BrowserApp) that hosts the browser control. Refer to the [OXPd Browser Use Model and UI Configuration Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-browser-use-model-and-ui-configuration-service-specification) documentation for more information.

## Native Dialogs
<div id="native-dialogs"></div>

The device may experience exceptions, such as paper jams, while the user is interacting with the web application via the browser UI. Just as with other built-in touchscreen applications, these events cause native modal device dialogs to display to the user. In these cases, the browser continues to run actively in the background and any JavaScript code continues to execute in the browser during this period of time. However, the user is not able to interact with the browser while the modal dialog is active. After the exception condition subsides or the native UI has been dealt with appropriately, including dismissal of the dialog, the browser resumes running as the primary application in the control panel’s foreground.

# Sessions and Context Management
<div id="sessions-and-context-management"></div>

Sessions are used heavily in web application servers to manage multiple contexts across stateless, transient connections from multiple browser clients. Session IDs are generated by web applications and communicated through various mechanisms to web browsers (cookies, hidden form fields, encoded in the URL, etc.) so that those session IDs can be mapped back into the appropriate session on the server during subsequent browser requests. These IDs provide continuity across the otherwise stateless browser requests.

## Sessions and Stateless Web Service Connections
<div id="sessions-and-stateless-web-service-connections"></div>

There is support in some of the web service technology stacks for implicitly stateful web service approaches. Axis2/J supports SOAP session management, but Axis2/C does not.
WCF can be configured via an `aspCompatibility`` flag to support HTTP context, etc. There is also the use of `WS-Context` or the session flag in `WS-Addressing` to correlate messages implicitly.

However, SOA patterns favor coarse-grained operations that use coarse-grained parameters over stateless connections and discourage relying on implicitly stateful connections. The goal of the design is to take this SOA approach. This means that neither the connection itself nor the binding layer is directly involved in keeping track of operations or session information. Instead, any correlation or context for a particular method call is passed as data in the method explicitly. Examples of this are a job ID or some other kind of context ID.

## Device Sessions
<div id="device-sessions"></div>

As the device is opened to a new programming model, it is necessary to extend the awareness of walk-up sessions to the external application logic. For example, support for a scan service API on the device requires that the calling application be coordinated with any walk-up user that may be standing at the device. It is inadvisable to allow any external application to initiate a scan without first synchronizing walk-up context with the device.

Device sessions are mapped using context IDs that are described in more detail in “Device context ID”.

## OXPd Context Model
<div id="oxpd-context-model"></div>

This architecture recognizes sessions as part of the underlying service/data model rather than relying on a transport layer or other implicit mechanism to provide it. Decoupling these IDs from the transport layer reduces dependencies on particular SOAP stack implementations or configurations. Context is managed using the exchange of opaque context IDs (GUID strings) between the server and the device. There is no visibility from either party into the model that underlies the ID itself. These IDs are called context IDs.

### Device Context IDs
<div id="device-context-ids"></div>

Device context IDs are generated by the device and are passed by the device to the web application when it makes connections to the web server. Later, when the web application makes server API calls into the device, the device maps those service API calls to appropriate device context (walk-up user at touchscreen, etc.).

There are two types of device contexts:

- [UI Context IDs](#ui-context-ids)
- [Accessory Context IDs](#accessory-context-ids)

#### UI Context IDs
<div id="ui-context-ids"></div>

A UI context begins when the browser application is invoked and ends when the browser application exits. The UI context ID is passed by the browser through the HTTP header to the web application server. Depending on the device web API, subsequent calls made from the web application into the device require the correct context ID to allow the call into the device. If the ID is not correct, the call is rejected.

**Note:** This means that if the browser application is not displayed, there are certain web service APIs that cannot be called.

The diagram in “High-level system sequence with context IDs” depicts this model.

#### Accessory Context IDs
<div id="accessory-context-ids"></div>

Refer to the [OXPd Accessories Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-accessories-service-specification) documentation for more details on how this context ID works.

### Server IDs
<div id="server-ids"></div>

Server IDs help web applications to manage states across multiple invocations from the same device for the same workflow.
There are two types of Server IDs:

- [Server session IDs](#server-session-ids)
- [Server context IDs](#server-context-ids)

#### Server Session IDs
<div id="server-session-ids"></div>

Server session IDs and are generated by the web application. Server session IDs are usually mapped to a web application’s browser session ID and can be encoded for exchange between the browser and the web application in a variety of ways, including cookies, hidden form fields, parameters passed in the URL, etc.

- Subsequent requests from the browser contain the server session ID.
- The web application uses the server session ID to map the browser’s request to the appropriate instance of the web application.

#### Server Context IDs
<div id="server-context-ids"></div>

Server context IDs are freeform strings that are generated by a web application to be included in callback specifications.

- The server context ID is returned to the application on every instance of the callback.
- A server context ID can contain any data that might be useful to the web
application, such as the server session ID, the device’s IP address or hostname, etc.
- The web application uses the server context ID to map the device’s status
notification message to the appropriate instance of the web application.

### High-level System Sequence with Context IDs
<div id="high-level-system-sequence-with-context-ids"></div>

The following diagram shows a system-level sequence that illustrates the components in the system with focus on where context IDs are exchanged between the device and the web application.

<br><img src="../assets/OXPdArchitecturalOverviewSpec-SystemSequenceContextID.png" alt="OXPdArchitecturalOverviewSpec-SystemSequenceContextID" width="80%">
<br>*System Sequence Diagram with Context IDs*
<br>

# Binding Model
<div id="binding-model"></div>

Previous versions of OXPd have been based on the simple exchange of XML between HTTP endpoints. This model is easy to implement and fits the previous OXPd model, but is not the best fit for a more comprehensive API model.

One of the goals of this version of OXPd is that it is natural to program using standard tools.
As such, the goal for this release is to support a SOAP stack (V1.2) across the device fleet.
This approach supports standard web tools based on .NET, JAVA, or native.
SOAP v1.1 is not supported.

The WSDL/XSD schemas are the contract and WSDL/WS tool chains are used to generate code that used by the application.

This binding uses SOAP faults to express errors. See the accompanying API reference specification (*OXPdLib.chm*) included in the OXPd .NET/Java SDK [Download](https://developers.hp.com/hp-oxp/partners/developer-packages) for more information.

# Device services
<div id="device-services"></div>

This section provides a brief overview of the services and what they are used for:

- [`Test` Service](#test-service)
- [`CertificateManagement` Service](#certificatemanagement-service)
- [`UIConfiguration` Service](#uiconfiguration-service)
- [`Scan` Service](#scan-service)
- [`Accessories` Service](#accessories-service)
- [`DeviceInfo` Service](#deviceinfo-service)
- [`Authentication` Service](#authentication-service)
- [`Authorization` service](#authorization-service)
- [`Statistics` Service](#statistics-service)
- [`Quota` Service](#quota-service)
- [`Copy` Service](#copy-service) 

Refer to the linked service specifications for detailed service APIs. 

## `Test` Service
<div id="test-service"></div>

The `Test` service provides a set of simple methods to help developers test their environment’s support of OXPd synchronous and asynchronous (callback) communication patterns. It also provides a set of methods to enable automated testing of OXPd-based solutions.

Refer to the [OXPd Test Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-test-service-specification) for more information.


## `CertificateManagement` Service
<div id="certificatemanagement-service"></div>

The `CertificateManagement` service provides methods for managing Certificate Authority (CA) certificates to be used by the device to authenticate servers when making outbound SSL connections.

Refer to the [OXPd Certificate Management Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-certificate-management-service-specification) for more information.

## `UIConfiguration` Service
<div id="uiconfiguration-service"></div>

The `UIConfiguration`` service provides methods for managing the device's embedded browser and home screen buttons. Using this service, a web application can:

- Get the user interface profile for a device.
- Configure the browser settings on a device.
- Configure the trusted sites for the browser.
- Configure the top level (home screen) buttons.

The service also supports testing methods that manipulate the browser programmatically.

Refer to the [OXPd Browser Use Model and UI Configuration Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-browser-use-model-and-ui-configuration-service-specification) for more information.

## `Scan` Service
<div id="scan-service"></div>

The Scan service is used to initiate and interact with scan jobs in the device. Using this service, a web application can execute the following tasks:

- Query the scanner's capabilities and defaults.
- Create and validate scan tickets.
- Start, monitor, and cancel scan jobs.

Refer to the [OXPd Scan Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-scan-service-specification) for more information.

## `Accessories` Service
<div id="accessories-service"></div>

The `Accessories` service provides remote access to USB accessories that are plugged into the device. Using this service, a web application can execute the following tasks:

- Manage accessory registration records.
- Enumerate accessories.
- Read data from accessories.
- Write data to accessories.

Accessories must be registered before they can be used with this service.

Refer to the [OXPd Accessories Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-accessories-service-specification) for more information.

## `DeviceInfo` Service
<div id="deviceinfo-service"></div>

The `DeviceInfo` service is used to retrieve basic device information from the device.

Refer to the [OXPd Device Info Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-device-info-service-specification) for more information.

## `Authentication` Service
<div id="authentication-service"></div>

The `Authentication` service is used to register server-based authentication agents with the device.

Refer to the [OXPd Authentication Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-authentication-service-specification) for more information.

## `Authorization` service
<div id="authorization-service"></div>

The `Authorization` service is used to register a server-based authorization agent with the device.

Refer to the [OXPd Authorization Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-authorization-service-specification) for more information.

## `Statistics` Service
<div id="statistics-service"></div>

The `Statistics` service is used to register server-based statistics agents with the device.

Refer to the [OXPd Statistics Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-statistics-service-specification) for more information.

## `Quota` Service
<div id="quota-service"></div>

The `Quota` service is used to register a server-based quota agent with the device.

Refer to [OXPd Quota Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-quota-service-specification) for more information.

## `Copy` Service
<div id="copy-service"></div>

The `Copy` service provides remote access to a device’s native copy job service. 

Refer to the [OXPd Copy Service](https://developers.hp.com/oxpd-netjava/doc/oxpd-copy-service-specification) for more information.

# Quality of service best practices
<div id="quality-of-service-best-practices"></div>

Compared with prior versions of OXPd, this new architectural model provides the application developer with substantially more flexibility and control over the workflow application.
However, with this additional control comes additional responsibility for the developer to pay closer attention to several aspects of the application relating to quality of service.
The following table is taken from the *Java Enterprise System Deployment Planning Guide*.

| Quality | Description |
|---|---|
| Performance | The measurement of response time and throughput with respect to user load conditions. |
| Availability | A measure of how often a system’s resources and services are accessible to end users, often expressed as the uptime of a system. |
| Scalability | The ability to add capacity (and users) to a deployed system over time. Scalability typically involves adding resources to the system but should not require changes to the deployment architecture. |
| Security | A complex combination of factors that describe the integrity of a system and its users. Security includes authentication and authorization of users, security of data, and secure access to a deployed system. |
| Latent capacity | The ability of a system to handle unusual peak loads without additional resources. Latent capacity is a factor in availability, performance, and scalability qualities. |
| Serviceability | The ease by which a deployed system can be maintained, including monitoring the system, repairing problems that arise, and upgrading hardware and software components. |

As the sophistication level of the workflow application increases to include advanced business critical document processing actions on behalf of a large set of devices, these factors become increasingly important.

## Example: QOS Considerations for Document Preview
<div id="example-qos-considerations-for-document-preview"></div>

As the OXPd architecture adds new user interface capabilities, completely new workflow paradigms are enabled. For example, the ability to quickly scan a document to the server and then show a document preview from the server-side. Using this example, if a particular application had previously not provided this kind of document preview feature, the addition of this feature could substantially change the characteristics of the application.

Take into consideration, for example, that the previous version of the application:

1. Used OXPd 1.4 to capture some basic information from the user through an OXPd
XML configuration file.
2. Used the OXPd 1.4 XML configuration file to scan a document into a folder on the
application server.
3. Performed some processing steps on the scanned document and forwarded the
resulting document to a back-end document repository.

Now assume that the new version of the application:

1. Uses OXPd 1.6 to interact with the user through the browser.
2. Executes a scan, where JPEG images are sent from the device to an HTTP destination
on the web application server.
3. Uses the browser to display low-resolution images representing the pages of the
scanned document.
4. Allows the user to perform additional operations on the document on the application
server.
5. Performs additional processing steps on the document and forwards the resulting
document to a back-end document repository.

The solution must now account for the fact that it is having a richer UI conversation with the device’s embedded browser. For instance, rather than sending high-resolution scanned images back to the device for a browser-based preview, the web application would likely subsample the original scan down to lower-resolution thumbnail images intended only for display.

Among other elements that require consideration, the new version of the solution must be analyzed relative to the new capabilities and determine:

- What kind of server hardware is now required to run this application?
- What is the network bandwidth of this application?
- How many devices can a single server support?
- Does the AJAX-based web application display screens responsively? Does it gracefully
handle asynchronous screen image updates in the background without causing an unresponsive UI?
- How does the installation team scale up when more devices are added to the fleet?
- How does this profile vary during normal usage times, peak times, etc.
- When the web application software is being updated, what is the experience at the
MFP device if a user walks up and tries to use it?

This is example of OXPd specification describes how to interface with the device fleet, but it does not directly address the server-side issues associated with the solution.


# Target Platforms
<div id="target-platforms"></div>

OXPd supports all devices referred to in the [SDK Compatibility Guide](https://developers.hp.com/oxpd-netjava/doc/sdk-compatibility-guide) documentation. 

**Warning**: Download proper device firmware version for each product.
