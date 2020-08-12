---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the Hippocrates Platform API Documentation!

For testing the API, we suggest using one of the following tools:

* [Postman](https://www.getpostman.com/)
* [Insomnia REST Client](https://insomnia.rest/)


# Basic Operations

## API version

The API is versioned, in order to support backwards-incompatible changes. The current version is **v0**.

Base URLs:

Sandbox: [http://46.101.235.194/api/v0/](http://46.101.235.194/api/v0/)<br />
Production: -

## HTTP Verbs

Common HTTP request methods:

* **GET** - Get a resource or a list of resources. If successfull a 200 OK status code will be returned along with the results. A GET request is safe and never modifies data on the server and can always be retried if a network error occurs.
* **PUT** - The PUT method completely replaces whatever currently exists at the target URL with something else.
* **PATCH** - Update an existing resource with new values. The request can contain a partial representation of the resource, meaning only the provided fields/attributes will be updated.
* **POST** - Create a new resource or trigger a process with side-effects such as importing asset level data. If a new resource is created a 201 Created status code will be returned along with the new resource. If a more complex action is successfully trigged a 200 OK status will be returned along with action specific details. If an error occurs doing a POST request the action may or may not have been completed and so retrying it may trigger additional side effects or attempt to create additional resources.
* **DELETE** - Delete an existing resource. If successful 200 OK status code will be returned along with the last snapshot of the resource. DELETE requests are idempotent and can safely be retried for the same effect.


## HTTP Status Codes
Code | Detail | Description
-------------- | -------------- | --------------
200 | OK | Command was a success. The response body may include the result.
201 | Created | Command was a success and a new resource has been created. The response body may include the result.
204 | No Content | Command was a success. There is no futher content available.
400 | Bad Request | The request was invalid. Often there is a missing parameter or validation errors. Error message with further information may be provided.
401 | Unauthorized | Authentication credentials were missing or invalid.
403 | Forbidden | The request was refused because your account has not permission to access endpoint.
404 | Not Found | The requested item does not exist.
429 | Too Many Requests | The request to create or update a resource resulted in validation errors. Error details are returned in the response body.
5xx | Serverside Errors | An error has occurred on our servers. Please wait a few minutes and try again or notify us about an error.


## Requests Encoding

Available Content-Types:

* application/json
* application/x-www-form-urlencoded
* multipart/form-data


## Date Format

All date and datetime values send to the API must be ISO 8601 formatted.

* Date format: YYYY-MM-DD (e.g. 2020-03-12)
* Datetime format: YYYY-MM-DDThh:mm:ss (e.g. 2005-08-09T18:31:42)


# Authentication

## Endpoints
API uses Json Web Token ([JWT](https://en.wikipedia.org/wiki/JSON_Web_Token)) for authorization. 
> Get token example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"email": "davidattenborough", "password": "boatymcboatface"}' \
  http://localhost:8000/api/v0/auth/token/
```

> If username and password are correct will be returned: 

```shell
{
  "access":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX3BrIjoxLCJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiY29sZF9zdHVmZiI6IuKYgyIsImV4cCI6MTIzNDU2LCJqdGkiOiJmZDJmOWQ1ZTFhN2M0MmU4OTQ5MzVlMzYyYmNhOGJjYSJ9.NHlztMGER7UADHZJlxNG0WSi22a2KaYSfd1S-AuT7lU",
  "refresh":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX3BrIjoxLCJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImNvbGRfc3R1ZmYiOiLimIMiLCJleHAiOjIzNDU2NywianRpIjoiZGUxMmY0ZTY3MDY4NDI3ODg5ZjE1YWMyNzcwZGEwNTEifQ.aEoAYkSJjoWH1boshQAaTkf8G3yn0kapko6HFRt7Rh4"
}
```

> You can use the returned access token to prove authentication for a protected view:

```shell
curl \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX3BrIjoxLCJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiY29sZF9zdHVmZiI6IuKYgyIsImV4cCI6MTIzNDU2LCJqdGkiOiJmZDJmOWQ1ZTFhN2M0MmU4OTQ5MzVlMzYyYmNhOGJjYSJ9.NHlztMGER7UADHZJlxNG0WSi22a2KaYSfd1S-AuT7lU" \
  http://localhost:8000/api/v0/some-protected-view/
```

> Refresh token usage

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"refresh":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX3BrIjoxLCJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImNvbGRfc3R1ZmYiOiLimIMiLCJleHAiOjIzNDU2NywianRpIjoiZGUxMmY0ZTY3MDY4NDI3ODg5ZjE1YWMyNzcwZGEwNTEifQ.aEoAYkSJjoWH1boshQAaTkf8G3yn0kapko6HFRt7Rh4"}' \
  http://localhost:8000/api/v0/auth/token/refresh/
```
Endpoint | Methods | Description
-------------- | -------------- | --------------
/auth/token/ | POST | Accepts the user's email and password and returns access and refresh tokens.
/token/refresh/ | POST | When short-lived access token expires, you can use the longer-lived refresh token to obtain another access token.

# API Objects

## Base objects

> GET list example:

```shell
curl \
  -X GET \
  http://localhost:8000/api/v0/objects/
```

> Response:

```shell
{
  "count": 300,
  "next": "http://localhost:8000/api/v0/objects/?page=2",
  "previous": null,
  "results": [
      {...object 1...},
      {...object 2...}
  ]
}
```

> POST new object example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{value_1: "Some value", value_2: "Some value"}' \
  http://localhost:8000/api/v0/objects/
```

> Success response:

```shell
# Status code 200
{
  "value_1": "Some value",
  "value_2": "Some value"
}
```

> If there any validation errors, will be returned:

```shell
# Status code 400
{
  "some_required_value": [
    "This field is required."
  ],
}
```

> GET specific object by ID example:

```shell
curl \
  -X GET \
  -H "Content-Type: application/json" \
  -d '{value_1: "Some value", value_2: "Some value"}' \
  http://localhost:8000/api/v0/objects/1/
```

> Response:

```shell
{
  "value_1": "Some value",
  "value_2": "Some value",
  "some_required_value": "Some value"  
}
```

> PUT example:

```shell
curl \
  -X PUT \
  -H "Content-Type: application/json" \
  -d '{value_1: "New value", value_2: "Some value", "some_required_value": "Some value"}' \
  http://localhost:8000/api/v0/objects/1/
```

> PATCH example:

```shell
curl \
  -X PATCH \
  -H "Content-Type: application/json" \
  -d '{value_1: "New value"}' \
  http://localhost:8000/api/v0/objects/1/
```

> Response:

```shell
{
  "value_1": "New value",
  "value_2": "Some value",
  "some_required_value": "Some value"  
}
```

> Use DELETE method to destroy object:

```shell
curl \
  -X DELETE \
  http://localhost:8000/api/v0/objects/1/
```

> If object will deleted successfully, will be returned response with status code 204 (No Content)

Each type of object has standart endpoints structure:

Endpoint | Methods | Description
-------------- | -------------- | --------------
/objects/ | GET, POST | This endpoint uses to get list of objects (using GET method) and to create new objects (using POST method).
/objects/{ID}/ | GET, PUT, PATCH, DELETE | Uses to get specific object by ID (GET), update object (PUT), partial update (PATCH) and delete the object (DELETE).

## Pagination

> Pagination example:

```shell
curl \
  -X GET \
  http://localhost:8000/api/v0/objects/?page=2&page_size=1
```
> Response:

```shell
{
  "count": 300,
  "next": "http://localhost:8000/api/v0/objects/?page=3&page_size=10",
  "previous": "http://localhost:8000/api/v0/objects/?page=1&page_size=10",
  "results": [
      ... Some objects ...
  ]
}
```

For endpoints that returns many objects, you can use those GET query params:

Query param | Type | Description
-------------- | -------------- | -----------
page | int | Set this param to request specific page.
page_size | int | Use this param to set limit of objects per page.<br />Default = 100 objects per page.

## Ordering

> Ordering example:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?ordering=some_field

```

> Descending ordering example:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?ordering=-some_field

```

> Multifield ordering example:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?ordering=some_field,-other_field

```

You can use **'ordering'** URL param to set ordering for object list. <br />
Fields that can be used for ordering you can find in **'Ordering'** column of data table for each object type.

As default API use ascending ordering. If you need descending ordring you should add minus as field name prefix **'-'**.


## Filtering

> Simple filter example:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?some_field=value&other_field=value

```

To filter object list you can use filter field name as URL param. <br />
Fields that can be used to filter list you can find in **'Filtering'** column of data table for each object type.

## Searching

> Simple search example:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?search=value

```

To search objects use **'search'** URL param. <br />
Fields that will be used to search objects you can find in **'Searching'** column of data table for each object type.


# Specializations

Specializations objects describe doctors specialization and related with User objects.<br />

## Endpoints

Endpoint | Methods | Description
-------------- | -------------- | --------------
/specializations/ | GET, POST | Specializations list endpoint.
/specializations/{ID}/ | GET, PUT, PATCH, DELETE | Specific Specialization detail endpoint.

## Data

> Get specializations list example:

```shell
{
    "count": 5,
    "next": null,
    "previous": null,
    "results": [
        {
            "id": 1,
            "name": "Хирург",
            "secondary_name": "Хирургия",
            "banner": "http://domain.com/media/specializations/banners/banner_1.png",
            "main": false,
            "main_on_categories_page": false,
            "sort_id": 1,
            "created": "2020-08-07T03:10:24.258973",
            "updated": "2020-08-07T03:10:24.259024"
        },
        {
            "id": 2,
            "name": "Офтальмолог",
            "secondary_name": "Офтальмология",
            "banner": "http://domain.com/media/specializations/banners/banner_2.png",
            "main": true,
            "main_on_categories_page": false,
            "sort_id": 2,
            "created": "2020-08-07T03:10:40.671968",
            "updated": "2020-08-07T03:10:40.672002"
        }
    ]
}
```

Field name | Type | Is required | Read only | Default value | Ordering | Filtering | Searching
-------------- | -------------- | -------------- | -------------- | -------------- | -------------- | -------------- | --------------
name | string | yes | no | | + | | + 
secondary_name | string | yes | no | | + | | +
banner | file / image url | no | no
main | boolean | yes | no | false | + | + |
main_on_categories_page | bool | yes | no | false | + | + |
sort_id | int | yes | no | | + | + |
created | datetime | yes | yes | | + | + |
updated | datetime | yes | yes | | + | + |

# Users

## Endpoints

Endpoint | Methods | Description
-------------- | -------------- | --------------
/users/register/ | POST | New users registration endpoint.
/users/confirmation/{uid}/{token}/ | GET | Email address confirmation endpoint.<br />{uid} - users encoded ID;<br />{token} - confirmation token generated by platform.
/users/ | GET, POST | Users list endpoint.
/users/{ID}/ | GET, PUT, PATCH, DELETE | User detail endpoint.
/me/ | GET, PUT, PATCH | Detail of current authorized user. Use this endpoint to get or update current user information.

## Registration
> Register new user example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "email": "test@example.com",
    "password": "password",
    "first_name": "Vladimir",
    "last_name": "Ivanov",
    "parent_name": "Petrovich",
    "phone": "380998887766",
    "work_phone": "0445453234",
    "birth_date": "1992-03-03",
    "region": "Kyiv",
    "town": "Kyiv",
    "college": "NAU",
    "diploma": "KO034123123",
    "job_place": "Central Hospital",
    "job_name": "Dentist",
    "specialization": 1
  }
  ' \
  http://localhost:8000/api/v0/users/register/
```

> Success response:

```shell
# Status code 201
{
  "email": "test@example.com",
  "first_name": "Vladimir",
  "last_name": "Ivanov",
  "parent_name": "Petrovich",
  "phone": "380998887766",
  "work_phone": "0445453234",
  "birth_date": "1992-03-03",
  "region": "Kyiv",
  "town": "Kyiv",
  "college": "NAU",
  "diploma": "KO034123123",
  "job_place": "Central Hospital",
  "job_name": "Dentist",
  "specialization": 1,
  "email_confirmed: false
} 
```

To register new user, POST user data to **/api/v0/users/register/** endpoint.<br />
After success registration the platform will send confirmation link to user's email.<br />
When user will visit confirmation link - **email_confirmed** field value will be changed to 'true'.


## Data

Field name | Type | Is required| Read only | Description | Ordering | Filtering | Searching
-------------- | -------------- | -------------- | -------------- | -------------- | -------------- | -------------- | --------------
email | string | yes | no | Email also used as username | + | + | + 
first_name | string | yes | no | | + | + | + 
last_name | string | yes | no | | + | + | + 
parent_name | string | no | no | | + | + | + 
phone | string | yes | no | | + | + | + 
work_phone | string | no | no | | + | + | + 
birth_date | date | no | no
region | string | yes | no | | + | + | + 
town | string | yes | no | | + | + | + 
college | string | no | no | | + | + | + 
diploma | string | no | no | | + | + | + 
job_place | string | yes | no | | + | + | + 
job_name | string | yes | no | | + | + | + 
specialization | int | yes | no | [Specializations](#specializations) object ID
email_confirmed | bolean | yes  | yes | | + | + |


# Events

## Endpoints

> Get events list example:

```shell
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": [
        {
          "id": 1,
          "name": "Event in Kyiv",
          "description": "Event description",
          "certificate_template": 1,
          "start_date": "2019-09-15T14:00:00",
          "end_date": "2019-09-15T15:00:00",
          "testing_end_date": "2019-09-15T16:00:00",
          "image": "http://example.com/...png",
          "text": "Full event text",
          "youtube_link": "https://youtube.com/..."
        },
        {
            "id": 2,
            "name": "Event in Lviv",
            "description": "Event description",
            "certificate_template": 2,
            "start_date": "2019-09-15T14:00:00",
            "end_date": "2019-09-15T15:00:00",
            "testing_end_date": "2019-09-15T16:00:00",
            "image": "http://example.com/...png",
            "text": "Full event text",
            "youtube_link": "https://youtube.com/..."
        }
    ]
}
```

Endpoint | Methods | Description
-------------- | -------------- | --------------
/events/ | GET, POST | Events list endpoint.
/events/{ID}/ | GET, PUT, PATCH | Event details.
/events/{ID}/test/ | GET | Event test instance details.
/events/{ID}/test/questions/ | GET, POST | Test questions list.
/events/{ID}/test/questions/{ID}/ | GET, PUT, PATCH | Test question details.
/events/{ID}/test/questions/{ID}/answers/ | GET, POST | Test question answers list.
/events/{ID}/test/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Test question answers detail.


## Data

Field name | Type | Is required | Read only | Default value
-------------- | -------------- | -------------- | -------------- | --------------
name | string | yes | no
description | string | yes | no
text | string | yes | no
certificate_template | int (ID) | no | no
image | file / image url | no | no
start_date | datetime | yes | no 
end_date | datetime | yes | no 
testing_end_date | datetime | yes | no
is_draft | boolean | yes | no | true
private | boolean | yes | no | false
youtube_link | string | no | no
created | datetime | yes | yes
updated | datetime | yes | yes