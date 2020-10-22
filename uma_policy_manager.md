



# UMA Policy Manager  

| | |
|:---|:---|
|Version:|0.1|
|Document Date:|2020-07-30|
|Editors:|Alec Laws|
|Contributors:|Kate Downing|
|Produced by:|
|Status| |
|Abstract| |
|Suggested Citation| |

#### Notice
IPR Option: Non-Assertion Covenant
Copyright: The content of this document is copyright of Kantara Initiative, Inc.© 2020 Kantara Initiative, Inc.





## Abstract

This extension to UMA enables a resource owner to use a policy management service to manage the policy conditions, such as required requesting party claims, over their registered resources in order for the authorization server to grant access to a client.


## 1. Introduction

This document extends [UMA Fedz] in order to specify the interface provided by the Authorization Server (AS) to the Resource Owner (RO) for policy management. This is achieved by introducing a Policy API specification and client type which represents the RO: the UMA Policy Manager.

This new client type is authorized to use the Policy API after being authorized by the RO as the AS. THis is the same mechanism used by a Resource Server (RS) to put resources under protection, the RS is issued a Personal Access Token (PAT) by the AS.


This new client provides the following key benefits to an UMA ecosystem:
- The AS may delegate policy management user experience and advice to another party;
- The RO is able to choose this client, where they may not be able to choose an AS or RS;
- The RO receives a consistent policy management interface;
- The RO may manage resources across many AS's allowing for a complete view of the RO's resources and policy.


For example, there are two UMA AS's, one operated by Alice's local health authority and one operated by Alice's personal bank. Alice's Resource Servers are only able to accept authorization decisions from those AS's; effectively Alice must use those AS's to protect her resources. When those AS's support this profile, they return some agency to Alice in how she manages her resources and policy, and reduce their own need to provide this User Experience (UX) (as perhaps each AS provides an UMA Policy Manager by default, but also accepts third-party policy management services). Alice may then manage policy across all of her RS's and AS's from a single control panel. 



### 1.1 Notational Conventions

### 1.2 Abstract Flow

```
      +------------------+ 
      |     Resource     | 
      |       Owner      | 
      +------------------+ 
             |              
             |   
             v 
      +------------------+
      |     Policy       |
      |     Manager      |
      +------+-----------+
             |       
             |       
          manage (perhaps via a Personal Access Token type authz?)   
             |              
             |              
             v              
       +-----------+   
       |   Policy  |   
       |    API    |   
       +-----------+   
       +---------------+--+
       |                  |
       |   Authorization  |
       |      Server      |
       |                  |
       +------------------+
```

This specification uses all of the terms and concepts in [UMAGrant] and [UMA Fedz]. This figure introduces the following new concepts:

- UMA Policy Manager
- Policy API:	The API presented by the Authorization Server to the UMA Policy Manager. This API is OAuth protected



### 1.3 HTTP Usage, API Security, and Identity Context


## 2 Authorization Server Metadata 

TBD


## Registered Resource Endpoint

The API available at the registered resources endpoint enables the Policy Manager to have knowledge of the resources under protection for the resource owner. 


### Registered Resource Description

A registered resource is a JSON document that extends the resource description from [UMA Fedz 3.1 Resource Description](1) with the following parameters:

resource_server   REQUIRED    A string identifying the resource server that hosts this resourcce

(1 https://docs.kantarainitiative.org/uma/wg/rec-oauth-uma-federated-authz-2.0.html#resource-set-desc)

### Registered Resource API

The authorization server must support the following resource management options, and must require a value Resource Management Token (RMT) for access to them. Here, regresuri stands for the registered resource endpoint and \_id stands for the authorization server-assigned identifier for the web resource corresponding to the resource at the time it was created, included within the URI returned in the location header.

- Read resource description: GET regresuri/\_id
- List resource descriptions: GET regresuri/

#### Read Registered Resource 

Reads a previously registered resource description using the GET method. If the request is successful, the authorization server MUST respond with an HTTP 200 status message that includes a body containing the referenced resource description, along with an \_id parameter.

Form of a read request, with a RMT in the header:
```
GET /regres/KX3A-39WE HTTP/1.1
Authorization: Bearer 4B7E-937E-FD64DA6F4C92
...
```

Form of a successful response, containing all the parameters that were registered as part of the description:

```
HTTP/1.1 200 OK
Content-Type: application/json
...
{  
   "_id":"KX3A-39WE",
   "resource_scopes":[  
      "read-public",
      "post-updates",
      "http://www.example.com/scopes/all"
   ],
   "icon_uri":"http://www.example.com/icons/sharesocial.png",
   "name":"Tweedl Social Service",
   "type":"http://www.example.com/rsrcs/socialstream/140-compatible",
   "resource_server": "https://tweedl.com"
}
```

#### List Registered Resource Description

Identical to the option defined in [UMA Fedz 3.2.5 List Resource Descriptions]()



## Policy Endpoint

The API available at the policy endpoint enables the Policy Manger to register Resource Owner requirements for claims presentation in order for a requesting party to be granted access to a protected resource.

The Policy Manager uses a RESTful API at the Authorization Server's policy endpoint to create, read, update and delete policy descriptions, along with retrieving lists of such descriptions. The descriptions consist of JSON documents that are maintained as web resources at the Authorization Server.


[INSERT DIAGRAM HERE]
Figure TBD illustrates the Policy API operations, with requests and success responses shown.





### Policy Description


A policy is a JSON document that describes a policy sufficiently for the Authorization Server to make a decision for a resource request. A policy document MAY be provided as a signed JSON Web Token (JWT) to ensure it's integrity to the RS during enforcement. A policy description has the following parameters:


- resource_id           REQUIRED    The resource id registered at the AS to whch this policy applies

- resource_scopes       REQUIRED    The approved scopes if the claims requirements are met

- required_claims       REQUIRED    An array of claims/attributes that must be presented by the RqP in order to access this resource under this policy. Claims may be presented to the AS through many token formats, or gathered through interactive claims gathering.


optional/extension/discussion:
- sub                   The subject known by the RS for this RO (useful for general resource definitions)
- resource_server       (again, useful for general resource definitions)
- client_id             The client application that may be used to access this resource


For example... 

```
{
  resoruce_id: "KX3A-39WE",
  "resource_scopes": [
      "read-public",
      "post-updates",
  ],
  required_claims: []
}
```


### Policy API 

The Authorization Server must support the following policy management options, and must require a value RMT for access to them. Here, poluri stands for the policy endpoint and \_id stands for the authorization server-assigned identifier for the web resource corresponding to the policy at the time it was created, included within the URI returned in the location header.

- Create policy: POST poluri/
- Read policy: GET poluri/_id
- Update policy: PUT poluri/_id
- Delete policy: DELETE poluri/_id
- List policy: GET poluri/








