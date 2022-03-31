# Eurus-api-specs Guidelines V-0.1

##  EURUS REST API Guidelines Working Group

Name | Name | Name |
---------------------------- | -------------------------------------- | ----------------------------------------
ruchira	                     |sumihiran	                              |   Anuda
gihan	                       |Nimna	                                  |Anjelo

<div style="font-size:150%">
Document editors: Anjelo <br/>
</div>

## 1. Abstract
EURUS REST API Guidelines, as a design principle, encourages application developers to have resources accessible to them via a HTTP interface. To provide the smoothest possible experience for developers on platforms following the EURUS REST API Guidelines, EURUS APIs SHOULD follow consistent design guidelines to make using them easy and intuitive.
This document establishes the guidelines EURUS APIs SHOULD follow so RESTful interfaces are developed consistently.
## 2. Table of contents
- [Eurus REST API Guidelines Working Group](#eurus-rest-api-guidelines-working-group)
- [1. Abstract](#1-abstract)
- [2. Table of contents](#2-table-of-contents)
- [3. Introduction](#3-introduction)
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
#### 4.4.1. POST
POST operations SHOULD support the Location response header to specify the location of any created resource that was not explicitly named, via the Location header.
As an example, imagine Place an Order -> create new resource

```http
POST http://localhost:7082/api/orders
```
