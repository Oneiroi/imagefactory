% IMAGEFACTORY REST API(1) Version 1.0 - February 10, 2012

Image Factory is the ideal system image creation engine for any application that needs to support a variety of virtualization and cloud services. Our REST API provides developers with a straightforward and easy way to develop solutions on top of Image Factory. This document describes the Image Factory REST API for building and pushing images as well as getting the status of builder operations.

## Starting imagefactory in REST mode

---

*   To use the REST API, imagefactory must be started with the `--rest` command line argument. 
    *   _DEFAULT_: imagefactory listens on port 8075.
    *   `--port` can be specified on the command line to change the port imagefactory listens on.
*   _DEFAULT_: imagefactory will use SSL and generate a self signed key. 
    *   `--no_ssl` can be specified on the command line to turn off SSL.
    *   `--ssl_pem` can be used on the command line to specify the path to a different certificate.
*   _DEFAULT_: imagefactory uses OAuth to authenticate connections.
    *   `--no_oauth` can be specified on the command line to turn off OAuth.
    *   More detail on how Image Factory uses OAuth can be found [below](#oauth)

**NOTE:** As an alternative to specifying arguments on the command line, options can be set in the imagefactory configuration file. Just leave the dashes off of the option name.

## Using the Image Factory REST API

---

To use the Image Factory REST API, send an HTTP request to any of the [resources][] Image Factory provides.  Each resource supports one or more of the stand HTTP methods (POST, GET, PUT, DELETE) which map to the operations Create, Read, Update, and Delete. More detail on what methods are supported and what parameters are required by each resource can be found in the [resources][] section.

Responses are formatted as JSON in all cases.  POST requests can also be formatted as JSON if the HTTP header, `Content-Type`, is set to `application/json`. Response contents are documented for each specific resource in the [resources][] section.


<a id="oauth"></a>
## OAuth Authentication

---

Image Factory uses two-legged OAuth to protect writable operations from unauthorized access. This means that even when OAuth is configured and enabled, Image Factory allows all read-only requests. This makes it simple to use any browser to get a quick status of current builder activity.

Any number of consumer_key / shared_secret pairs can be used. Just add these to the `clients` section of the `imagefactory.conf` file.

_Example:_  
    `"clients": {
        "client1": "our-secret",
        "client2": "just-between-us"
    }`

<a id="resources"></a>
## Resources

---

* __*/imagefactory*__  
    **Methods:**
    
    * **GET**

    > **Description:** Returns the version string for the API
    >
    > **OAuth protected:** NO
    >
    > **Parameters:**  
      
    > > __None__
    >
    > **Responses:**  
      
    > > __200__ - Name and version of the API  
    >
    > *Example:*  
    > `% curl http://imgfac-host:8075/imagefactory`
    > 
    > `{"version": "1.0", "name": "imagefactory"}`

* __*/imagefactory/base_images*__  
    **Methods:**

    * **POST**
    
    >  **Description:** Builds a new base image.  This is a cloud neutral image based on the provided template
    >
    > **OAuth protected:** YES
    >
    > **Parameters:**  
    
    > > __template__ - string representation of XML document, UUID, or URL 
    > > __callback_url__ - A REST endpoint to post status updates to 
    > > __parameters__ - additional parameters that may influence the build
    >
    > **Responses:**  
    
    > > __202__ - New base_image  
    > > __400__ - Missing parameters  
    > > __500__ - Error building image
    >
    > *Example:*  
    >  
        % curl -d "template=<template><name>f14jeos</name><os>   
        <name>Fedora</name> <version>14</version> <arch>x86_64</arch> <install  
        type='url'> <url>http://download.fedoraproject.org/pub/fedora/linux/re  
        leases/14/Fedora/x86_64/os/</url></install><rootpw>p@55w0rd!</rootpw>  
        </os><description>Fedora 14</description></template>"  
        http://imgfac-host:8075/imagefactory/images
    >
    >  
        {"_type": "base_image", "href": "http://imgfac-host:8075/imagefactory/base_images  
        /0e5b4e6b-c658-4a16-bc71-88293cb1cadf", "id": "0e5b4e6b-c658-4a16-bc71-  
        88293cb1cadf"}

* __*/imagefactory/target_images*__  
    **Methods:**

    * **POST**
    
    >  **Description:** Creates a new target image.  This is a cloud specific image based on
       either an existing base image or on a newly supplied template.
    >
    > **OAuth protected:** YES
    >
    > **Parameters:**  
    > > __base_image__ - UUID or URL 
    > > __template__ - string representation of XML document, UUID, or URL 
    > > __target__ - target cloud to build for 
    > > __callback_url__ - A REST endpoint to post status updates to 
    > > __parameters__ - additional parameters that may influence the build
    >
    > Note: Users must supply either a template or a base image but not both.
    
    > **Responses:**  
    
    > > __202__ - New target_image  
    > > __400__ - Missing or extra parameters  
    > > __500__ - Error building image
    >
    > *Example:*  
    >  
        % curl -d "base_image=0e5b4e6b-c658-4a16-bc71-88293cb1cadf&target=ec2"
    >
    >  
        {"_type": "target_image", "href": "http://imgfac-host:8075/imagefactory/target_images
        /bde09e1b-047a-4d6a-aaf8-363c7dbf3391", "id": "bde09e1b-047a-4d6a-aaf8-363c7dbf3391"}

* __*/imagefactory/provider_images*__  
    **Methods:**

    * **POST**
    
    >  **Description:** Creates a new provider image.  This is a launchable on-cloud image based
       on an existing target_image or on a template.
    >
    > **OAuth protected:** YES
    >
    > **Parameters:**  
    > > __target_image__ - UUID or URL 
    > > __template__ - string representation of XML document, UUID, or URL 
    > > __target__ - target cloud to build for 
    > > __snapshot__ - boolean indicating if this provider_image should be a snapshot-style image 
    > > __callback_url__ - A REST endpoint to post status updates to 
    > > __parameters__ - additional parameters that may influence the build
    >
    > Note: If snapshot is false, users must supply either a template or a target_image but not
      both.  If snapshot is true, users must only supply a template.
    
    > **Responses:**  
    
    > > __202__ - New provider_image  
    > > __400__ - Missing or extra parameters  
    > > __500__ - Error building image
    >
    > *Example:*  
    >  
        % curl -d "target_image=bde09e1b-047a-4d6a-aaf8-363c7dbf3391&target=ec2-us-west-2"
    >
    >  
        {"_type": "provider_image", "href": "http://imgfac-host:8075/imagefactory/provider_images
        /d813666d-4d5b-4e65-b140-8145db4c0715", "id": "d813666d-4d5b-4e65-b140-8145db4c0715"}


<!-- links -->
[resources]: #resources (Resources)
