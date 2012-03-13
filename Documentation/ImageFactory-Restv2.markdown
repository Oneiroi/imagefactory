% IMAGEFACTORY REST API(1) Version 2.0 EARLY DRAFT - March 12, 2012


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
    >
    > > __template__ - string representation of XML document, UUID, or URL  
    > > __callback_url__ - A REST endpoint to post status updates to  
    > > __parameters__ - additional parameters that may influence the build  
    >
    > **Responses:**  
    >
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
    >
    > **Responses:**  
    >
    > > __202__ - New target_image  
    > > __400__ - Missing or extra parameters  
    > > __500__ - Error building image
    >
    > *Example:*  
    >  
    >   % curl -d "base_image=0e5b4e6b-c658-4a16-bc71-88293cb1cadf&target=ec2"
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
    >
    > > __target_image__ - UUID or URL  
    > > __template__ - string representation of XML document, UUID, or URL  
    > > __target__ - target cloud to build for  
    > > __snapshot__ - boolean indicating if this provider_image should be a snapshot-style image  
    > > __callback_url__ - A REST endpoint to post status updates to  
    > > __parameters__ - additional parameters that may influence the build  
    >
    > Note: If snapshot is false, users must supply either a template or a target_image but not
      both.  If snapshot is true, users must only supply a template.
    >
    > **Responses:**  
    >
    > > __202__ - New provider_image  
    > > __400__ - Missing or extra parameters  
    > > __500__ - Error building image
    >
    > *Example:*  
    >  
        % curl -d "target_image=bde09e1b-047a-4d6a-aaf8-363c7dbf3391&target=ec2-us-west-2"
    >
    >  
    >  
       {"_type": "provider_image", "href": "http://imgfac-host:8075/imagefactory/provider_images
        /d813666d-4d5b-4e65-b140-8145db4c0715", "id": "d813666d-4d5b-4e65-b140-8145db4c0715"}


<!-- links -->
[resources]: #resources (Resources)
