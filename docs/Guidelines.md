# Eurus-api-specs Guidelines V-0.1

##  EURUS REST API Guidelines Working Group

Name | Name | Name |
---------------------------- | -------------------------------------- | ----------------------------------------
ruchira	                     |sumihiran	                              |   Anuda
gihan	                     |Nimna	                                  |Anjelo

<div style="font-size:150%">
Document editor: Anjelo <br/>
</div>

## 1. Abstract
EURUS REST API Guidelines, as a design principle, encourages application developers to have resources accessible to them via a HTTP interface. To provide the smoothest possible experience for developers on platforms following the EURUS REST API Guidelines, EURUS APIs SHOULD follow consistent design guidelines to make using them easy and intuitive.
This document establishes the guidelines EURUS APIs SHOULD follow so RESTful interfaces are developed consistently.
## 2. Table of contents
- [Eurus REST API Guidelines Working Group](#eurus-rest-api-guidelines-working-group)
- [1. Abstract](#1-abstract)
- [2. Table of contents](#2-table-of-contents)
- [3. Introduction](#3-introduction)
- [4.Consistency fundamentals](#4-consistency-fundamentals)
    - [4.1. Supported methods](#41-supported-methods)
      - [4.1.1. POST](#411-post)
      - [4.1.2. PATCH](#412-patch)
      - [4.1.3. GET](#413-get)
      - [4.1.4. DELETE](#414-delete)
    - [4.2. Error condition responses](#42-error-condition-responses)
    - [4.3. HTTP Status Codes](#43-http-status-codes)
    - [4.4. Client library optional](#44-client-library-optional)
- [5. CORS](#5-cors)
    - [5.1. Client guidance](#51-client-guidance)
- [6. Collections](#6-collections)
<!-- /TOC -->
## 3. Introduction 
Developers access most our Platform resources via HTTP interfaces. Although each service typically provides language-specific frameworks to wrap their APIs, all of their operations eventually boil down to HTTP requests. we must support a wide range of clients and services and cannot rely on rich frameworks being available for every development environment. Thus a goal of these guidelines is to ensure our APIs can be easily and consistently consumed by any client with basic HTTP support.
To provide the smoothest possible experience for developers, it's important to have these APIs follow consistent design guidelines, thus making using them easy and intuitive. This document establishes the guidelines to be followed by EURUS REST API developers for developing such APIs consistently.

The benefits of consistency accrue in aggregate as well; consistency allows teams to leverage common code, patterns, documentation and design decisions.
These guidelines aim to achieve the following:
- Define consistent practices and patterns for all API endpoints across TAKG
- Adhere as closely as possible to accepted HTTP best practices in the industry at-large.
- Make accessing EURUS Services via EURUS  interfaces easy for all application developers.
- Allow service developers to leverage the prior work of other services to implement, test and document EURUS endpoints defined consistently.
## 4. Consistency fundamentals
### 4.1. Supported methods
Operations MUST use the proper HTTP methods whenever possible, and operation idempotency MUST be respected. HTTP methods are frequently referred to as the HTTP verbs. The terms are synonymous in this context, however the HTTP specification uses the term method.

Below is a list of methods that EURUS services SHOULD support. Not all resources will support all methods, but all resources using the methods below MUST conform to their usage.

Method  | Description                                                                                                                
------- | --------------------------------------------------------------------------------------------------------------------------
GET	    |Return the current value of an object
POST	  |Create a new object based on the data provided, or submit a command
DELETE	|Delete an object
PATCH	  |Apply a partial update to an object

<small>Table 1</small>

#### 4.1.1. POST
POST operations SHOULD support the Location response header to specify the location of any created resource that was not explicitly named, via the Location header.
As an example, imagine Place an Order -> create new resource

```http
POST http://localhost:7082/api/orders
```
The response would be something like:

```http
201 Created
Location: http://localhost:7082/api/orders
```
#### 4.1.2. PATCH
PATCH has been standardized by IETF as the method to be used for updating an existing object incrementally . EURUS REST API Guidelines compliant APIs SHOULD support PATCH.
suppose the original resource has the following JSON representation:
```json
{
    "name":"gizmo",
    "category":"widgets",
    "color":"blue",
    "price":10
}
```
As an example, imagine Place an user -> make a partial update
```http
HTTP PATCH http://localhost:7082/api//user/0001
[{
    "name":"gizmo",
    "category":"widgets",
    "color":"blue",
    "price":10
}]
```
Here is a possible JSON merge patch for this resource:
```json
{
    "price":12,
    "color":null,
    "size":"small"
}
```
The response would be something like:

```http
202 Accepted
Location: http://localhost:7082/api/orders
```
#### 4.1.3. GET
In HTTP, response format SHOULD be requested by the client using the Accept header. This is a hint, and the server MAY ignore it if it chooses to, even if this isn't typical of well-behaved servers. Clients MAY send multiple Accept headers and the service MAY choose one of them.

```http
GET http://localhost:7082/api//user
Accept: application/json
```

The response would be something like:

```http
200 Ok
Location: http://localhost:7082/api/orders
```

#### 4.1.4. DELETE
If the delete operation is successful, the web server should respond with HTTP status code 204 (No Content), indicating that the process has been successfully handled, but that the response body contains no further information. If the resource doesn't exist, the web server can return HTTP 404 (Not Found).

As an example, imagine Place an Order -> Delete 001 resource
```http
DELETE  http://localhost:7082/api/orders/001
```

The response would be something like:
```http
204 No Content
Location: http://localhost:7082/api/orders
```
### 4.2. Error condition responses
For nonsuccess conditions, developers SHOULD be able to write one piece of code that handles errors consistently across different EURUS REST API Guidelines services. This allows building of simple and reliable infrastructure to handle exceptions as a separate flow from successful responses. The following is based on the  JSON spec.
The error response MUST be a single JSON object. This object MUST have a name/value pair named "error." The value MUST be a JSON object.
This object MUST contain name/value pairs with the names "code" and "message," and it MAY contain name/value pairs with the names "target," "details" and "innererror."
The value for the "message" name/value pair MUST be a human-readable representation of the error.

The value for the "target" name/value pair is the target of the particular error (e.g., the name of the property in error).
The value for the "details" name/value pair MUST be an array of JSON objects that MUST contain name/value pairs for "code" and "message," and MAY contain a name/value pair for "target," as described above. The objects in the "details" array usually represent distinct, related errors that occurred during the request. 
Error responses MAY contain annotations in any of their JSON objects.

We recommend that for any transient errors that may be retried, services SHOULD include a Retry-After HTTP header indicating the minimum number of seconds that clients SHOULD wait before attempting the operation again.

We recommend that for any transient errors that may be retried, services SHOULD include a Retry-After HTTP header indicating the minimum number of seconds that clients SHOULD wait before attempting the operation again.

##### ErrorResponse : Object
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`error` | Error | ✔ | The error object.

<small>Table 2</small>

##### Error : Object
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`code` | String | ✔ | One of a server-defined set of error codes.
`message` | String | ✔ | A human-readable representation of the error.
`target` | String |  | The target of the error.
`details` | Error[] |  | An array of details about specific errors that led to this reported error.
`innererror` | InnerError |  | An object containing more specific information than the current object about the error.

<small>Table 3</small>

##### InnerError : Object
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`code` | String |  | A more specific error code than was provided by the containing error.
`innererror` | InnerError |  | An object containing more specific information than the current object about the error.

<small>Table 4</small>

Example of "innererror":

```json
{
  "error": {
    "code": "BadArgument",
    "message": "Previous passwords may not be reused",
    "target": "password",
    "innererror": {
      "code": "PasswordError",
      "innererror": {
        "code": "PasswordDoesNotMeetPolicy",
        "minLength": "6",
        "maxLength": "64",
        "characterTypes": ["lowerCase","upperCase","number","symbol"],
        "minDistinctCharacterTypes": "2",
        "innererror": {
          "code": "PasswordReuseNotAllowed"
        }
      }
    }
  }
}
```
### 4.3. HTTP Status Codes
Standard HTTP Status Codes SHOULD be used; see the HTTP Status Code definitions for more information.

### 4.4. Client library optional
Developers MUST be able to develop on a wide variety of platforms and languages, such as Windows, Android,iso, C#, React.

Services SHOULD be able to be accessed from simple HTTP tools such as curl without significant effort.

## 5. CORS
Services compliant with the Eurus REST API Guidelines MUST support [CORS (Cross Origin Resource Sharing)][cors].
Services SHOULD support an allowed origin of CORS * and enforce authorization through valid OAuth tokens.
Services SHOULD NOT support user credentials with origin validation.
There MAY be exceptions for special cases.

### 5.1. Client guidance
Web developers usually don't need to do anything special to take advantage of CORS. All of the handshake steps happen invisibly as part of the standard XMLHttpRequest calls they make.

Many other platforms, such as .NET, have integrated support for CORS.

## 6. Collections
