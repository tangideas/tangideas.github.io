---
layout: post
title:  "Consistent API Design Notes"
author: <a href="https://github.com/fwso">James Tang</a>
date:   2016-12-12 12:01:01
categories:
  - Architecture
tags:
  - Restful
  - API
---

## Introduction

`API` in this post refers to `HTTP APIs`, such as [Facebook Graph APIs](https://developers.facebook.com/docs/graph-api). 
Any popular, or intend to be popular, web platform,  provides APIs for developers to share data, integrate functionalities, etc.

When designing HTTP APIs for yourself, there are many different kinds of architectural styles for HTTP APIs avaialble, 
such as `RESTful`, `SOAP`, `XML-RPC`, etc, and you may perfer self-defined style. 
No matter which style you prefer, the APIs are not guranteed to be useful and easy to use without consistent design principles.

Here I share my notes for designing APIs, does not confined to any specified architectural style. 

## Resource Model

APIs are actually the interfaces to access and manipulate the resources of your system, 
so the first to design APIs should be modeling your resources. A good model prototype may be your 
database schema. 

I prefer to use Object and Relation to modeling resources. Object and realtion should be defined
clearly, and have consistent representation anywhere.  

### Data Types

If you are using dynamic language, such as PHP, to implement the API, you'd better pay attention to
the data type of the resources and attributes representation in response.

Following two example can be parsed correctly in PHP, but it may cause trouble for other languages:

```json
{
	"id": 12345678901234
}
```

```json
{
	"id": "12345678901234"
}
```

In this case, you should define the datatype for `id` to be `int` or `string`, but can not `mixed`.

## Operations

Operations can be `read`, `write`, `create`, `update`, `delete`, `remove`, etc. But you should
not use different operation names for the same operation, such as `delete` and `remove`.

The operations for each resource have to be defined at both object and attribute level.

## Scope and permission

Resource and operations then have to be categorized into different `scopes` according to permission requirements.

## Endpoints

API endpoints with similar purpose should follow the same pattern(s).

### Good example

Following two examples use HTTP `POST` method to update the Collection and Book information. The endpoints
`http://api.example.com/collections/<CID>/update.json` and `http://api.example.com/books/<BOOK_ID>/update.json` have the save pattern with the resource ID represented in URL, and `update.json` as the suffix.

```
POST http://api.example.com/collections/<CID>/update.json

{
	"name":"New Name for Collection<CID>"
}
```

```
POST http://api.example.com/books/<BOOK_ID>/update.json

{
	"name": "New Name for Book<BOOK_ID>"
}

```

### Bad example

For Collection update like this:

```
POST http://api.example.com/collections/<CID>/update.json

{
	"name":"New Name for Collection<CID>"
}
```

While updating Book uses a different URL pattern:

```
POST http://api.example.com/books/update.json

{
	"id": <BOOK_ID>,
	"name": "New Name for Book<BOOK_ID>"
}
```

Or even use different methods:

```
GET http://api.example.com/books/update.json?id=<BOOK_ID>&name=New+Name+for+Book<BOOK_ID>
```

This will not only confuse the API users(developers), but also cause many unnecessary works to calling the APIs.

## Request

As the `Endpoints` section shows, besides the Endpoints should be defined consistently, the requesting
method, requesting parameters have to be defined consistently and concisely.

## Success Response

Successful reponse for different operations may be different, but similar operations should have similar
response structure.

### Good example

Responses for both `PUT http://api.example.com/collections/<CID>` and `PUT http://api.example.com/books/<BOOK_ID>` operations have same result structure:

```json
{
	"success": true
}
```

### Bad example

Response for `PUT http://api.example.com/collections/<CID>`:

```json
{
	"success": true
}
```

Response for `PUT http://api.example.com/books/<BOOK_ID>`:

```json
{
	"id": <BOOK_ID>,
	"name": "New book name for book<BOOK_ID>"
}
```

For update operations for different resources, the response should be expected to be same, but the second example response the updated object instead of the success or failure status of the operation. 

## Error Response

Whenever an error occured for a request, the error response should have consistent:

- response structure
- HTTP status or self-defined error code
- descriptive and helpful error message

