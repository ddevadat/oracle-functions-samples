# Function that creates an object in a bucket in Object Storage using the OCI Dotnet SDK

This function uses Resource Principals to securely authorize a function to make
API calls to OCI services using the [OCI Dotnet SDK](https://docs.oracle.com/en-us/iaas/tools/dotnet/latest/api/index.html).
It creates an object in a bucket in Object Storage and returns a message with a status.

The function calls the following OCI Dotnet SDK classes:
* [Resource Principals](https://docs.oracle.com/en-us/iaas/tools/dotnet/latest/api/Oci.Common.Auth.ResourcePrincipalAuthenticationDetailsProvider.html) to authenticate
* [Object Storage Client](https://docs.oracle.com/en-us/iaas/tools/dotnet/latest/api/Oci.ObjectstorageService.ObjectStorageClient.html) to interact with Object Storage

As you make your way through this tutorial, look out for this icon ![user input icon](./images/userinput.png).
Whenever you see it, it's time for you to perform an action.


## Prerequisites

1. Before you deploy this sample function, make sure you have run steps A, B 
and C of the [Oracle Functions Quick Start Guide for Cloud Shell](https://www.oracle.com/webfolder/technetwork/tutorials/infographics/oci_functions_cloudshell_quickview/functions_quickview_top/functions_quickview/index.html)
    * A - Set up your tenancy
    * B - Create application
    * C - Set up your Cloud Shell dev environment

2. Have your Oracle Object Storage Namespace available. This can be found by
logging into your [cloud account](https://console.us-ashburn-1.oraclecloud.com/),
under your user profile, click on your Tenancy. Your Object Storage Namespace
is shown there.


## List Applications 

Assuming you have successfully completed the prerequisites, you should see your 
application in the list of applications.

```
fn ls apps
```


## Create or Update your Dynamic Group

In order to use other OCI Services, your function must be part of a dynamic 
group. For information on how to create a dynamic group, refer to the 
[documentation](https://docs.cloud.oracle.com/iaas/Content/Identity/Tasks/managingdynamicgroups.htm#To).

When specifying the *Matching Rules*, we suggest matching all functions in a compartment with:

```
ALL {resource.type = 'fnfunc', resource.compartment.id = 'ocid1.compartment.oc1..aaaaaxxxxx'}
```


## Create or Update IAM Policies

Create a new policy that allows the dynamic group to `inspect objects` in
the functions related compartment.

![user input icon](./images/userinput.png)

Your policy should look something like this:
```
Allow dynamic-group <dynamic-group-name> to inspect objects in compartment <compartment-name>
```
e.g.
```
Allow dynamic-group demo-func-dyn-group to inspect objects in compartment demo-func-compartment
```
For more information on how to create policies, go [here](https://docs.cloud.oracle.com/iaas/Content/Identity/Concepts/policysyntax.htm).


## Review and customize the function

Review the following files in the current folder:

- [PutObjects.csproj](./PutObjects.csproj) specifies all the dependencies for your function
- [func.yaml](./func.yaml) that contains metadata about your function and declares properties
- [PutObjects.cs](./PutObjects.cs) which is your actual Dotnet function

The name of your function *oci-objectstorage-put-object-dotnet* is specified in [func.yaml](./func.yaml).


## Deploy the function

In Cloud Shell, run the *fn deploy* command to build the function and its dependencies as a Docker image, 
push the image to the specified Docker registry, and deploy the function to Oracle Functions 
in the application created earlier:

![user input icon](./images/userinput.png)

```
fn -v deploy --app <app-name>
```
e.g.
```
fn -v deploy --app myapp
```


## Test

![user input icon](./images/userinput.png)
```
echo -n <JSON-object> | fn invoke <app-name> <function-name>
```
e.g.
```
echo -n '{"objectName": "<object-name>","bucketName": "<bucket-name>","namespaceName":"<bucket-namespace>","content":"<content-string>" }' | fn invoke myapp oci-objectstorage-get-object-dotnet
```
You should see the contents of the object appear in the terminal.

## Monitoring Functions

Learn how to configure basic observability for your function using metrics, alarms and email alerts:
* [Basic Guidance for Monitoring your Functions](../basic-observability/functions.md)

