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

Sandbox: [https://sandbox-api.hippocrates.org.ua/api/v0/](https://sandbox-api.hippocrates.org.ua/api/v0/)<br />
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

## Filtering by date

> Get objects where start date greater than or equal to 2020-09-10T00:00 example:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?start_date__gte=2020-09-10T00:00

```

Filtering by dates is flexible. You can use double underscore **'__'** prefix in the end of field name with comparison abbreviation to compare values.

Available comparison prefixes:

* **gt** - greater than
* **lt** - less than
* **gte** - greater than or equal to
* **lte** - less than or equal to

Also you can use **range** prefix to get objects in selected date range

> Get objects where start date in range from 2020-09-01T00:00 to 2020-09-30T00:00:

```shell
curl -X GET http://localhost:8000/api/v0/objects/?start_date__range=2020-09-01T00:00,2020-09-30T00:00

```

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


# Testing

Events, Courses, Courses lessons and Webinars can has testing that user can pass.<br />
Objects that have connected tests can have settings fields for testings like:

* **success_percent** - indicates what percentage of correct answers must be for the test to be considered successful. **Default value for the platform is 70.0%**
* **max_tries** - indicates how much times user able to retry test. **If max_tries equal to zero - it means that user can retry test infinite times.**

## Test object

```shell
curl -X GET http://localhost:8000/api/v0/objects/test/

```

> Success response:

```shell
{
  "id": 1, 
  "created": "2020-08-31T18:47:28.818181",
  "questions_set": [...],
  "success_percent": 70.0,
  "max_tries": 3,
  "user_statistics": {
      "tries": 2,
      "test_available": true
  }
} 
```

> Create test example:

```shell
curl -X POST http://localhost:8000/api/v0/objects/test/

```

> Success response will return code 201 and test object.

Use object test detail endpoint to get test object, test setting sand users statistics or to create it (usint POST method).

User statistics show how much tries user used and is test available.

## Questions

```shell
curl -X GET http://localhost:8000/api/v0/objects/test/questions/

```

> Success response:

```shell
{
  "count": 3,
  "next": null,
  "previous": null,
  "results": [
    {
      "id": 1,
      "title": "What color is the red cap?",
      "multiple_answers": false,
      "created": "2020-08-31T18:51:41.191955",
      "updated": "2020-08-31T18:51:41.191988",
      "answers_set": [...]
    },
    {
      "id": 2,
      "title": "What shape is the circle",
      "multiple_answers": false,
      "created": "2020-08-31T18:52:10.180814",
      "updated": "2020-08-31T18:52:10.180855",
      "answers_set": [...]
    }
  ]
}
```

> Create question example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "title": "What color is the red cap?",
    "multiple_answers": false
  }
  ' \
  http://localhost:8000/api/v0/objects/test/questions/
```

Each test can has infinite questions.

To create question use test question endpoint

## Answers 

```shell
curl -X GET http://localhost:8000/api/v0/objects/test/questions/1/answers/

```

> Success response:

```shell
  {
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
      {
        "id": 1,
        "title": "Red",
        "is_correct": true,
        "created": "2020-08-31T18:52:54.659546",
        "updated": "2020-08-31T18:52:54.659572"
      },
      {
        "id": 2,
        "title": "Blue",
        "is_correct": false,
        "created": "2020-08-31T18:53:02.952530",
        "updated": "2020-08-31T18:53:02.952554"
      },
      {
        "id": 3,
        "title": "Yellow",
        "is_correct": false,
        "created": "2020-08-31T18:53:09.561816",
        "updated": "2020-08-31T18:53:09.561838"
      }
    ]
}
```

> Create answer example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "title": "Red",
    "is_correct": true
  }
  ' \
  http://localhost:8000/api/v0/objects/test/questions/1/answers/
```

Use test question answers ednpoint to get list of answers or create answers.

## Test results

```shell
curl -X GET http://localhost:8000/api/v0/objects/test/results/

```

> Success response:

```shell
[
    {
        "id": 1,
        "test": 1,
        "user": 1,
        "questions_count": 3,
        "correct_answers": 1,
        "result": 33.33,
        "created": "2020-08-31T18:56:51.486344",
        "success": false
    },
    {
        "id": 2,
        "test": 1,
        "user": 1,
        "questions_count": 3,
        "correct_answers": 2,
        "result": 66.66,
        "created": "2020-08-31T18:58:03.667621",
        "success": false
    },
    {
        "id": 3,
        "test": 1,
        "user": 1,
        "questions_count": 3,
        "correct_answers": 3,
        "result": 100.0,
        "created": "2020-08-31T19:03:11.216353",
        "success": true
    }
]
```

> Data structure for user answers:

```shell
{
  "<question ID>": "<answer_ID>",
  "<question ID>": ["<answer_ID>", "<answer_ID>"] <- multiple answers
}
```

> Send test result to API example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "1": "4",
    "2": "5",
    "3": ["12", "13"]
  }
  ' \
  http://localhost:8000/api/v0/objects/test/results/
```

Use test results endpoint verify users answers.

**Notes:**

* If test is not available for user, endpoint will return 403 for POST request.

## Test Certificates

```shell
curl -X GET http://localhost:8000/api/v0/objects/test/results/certificate/

```

If user has successfull test result, he can get certificate using test certificate ednpoint.

**Notes:**

* If user hasn't test result with "success" = true, certificate endpoint will return code 404.


# Polls
Polls - are similar to Test objects, but polls can be passed only once by user and have no marks/certificates.<br />
Polls used to store statistics for platform clients.<br />

## Poll object

```shell
curl -X GET http://localhost:8000/api/v0/objects/poll/

```

> Success response:

```shell
{
  "id": 1, 
  "created": "2020-08-31T18:47:28.818181",
  "questions_set": [...],
  "users_statistics": {
    "poll_available": true
  }
} 
```

> Create poll example:

```shell
curl -X POST http://localhost:8000/api/v0/objects/poll/

```

> Success response will return code 201 and test object.

Use object poll detail endpoint to get poll object or to create it (usint POST method).

## Questions

```shell
curl -X GET http://localhost:8000/api/v0/objects/poll/questions/

```

> Success response:

```shell
{
  "count": 1,
  "next": null,
  "previous": null,
  "results": [
    {
      "id": 1,
      "title": "What color is the red cap?",
      "multiple_answers": false,
      "created": "2020-08-31T18:51:41.191955",
      "updated": "2020-08-31T18:51:41.191988",
      "answers_set": [...]
    }
  ]
}
```

> Create question example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "title": "What color is the red cap?",
    "multiple_answers": false
  }
  ' \
  http://localhost:8000/api/v0/objects/poll/questions/
```

Each poll can has infinite questions.

To create question use poll question endpoint

## Answers 

```shell
curl -X GET http://localhost:8000/api/v0/objects/poll/questions/1/answers/

```

> Success response:

```shell
  {
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
      {
        "id": 1,
        "title": "Red",
        "is_correct": true,
        "created": "2020-08-31T18:52:54.659546",
        "updated": "2020-08-31T18:52:54.659572"
      },
      {
        "id": 2,
        "title": "Blue",
        "is_correct": false,
        "created": "2020-08-31T18:53:02.952530",
        "updated": "2020-08-31T18:53:02.952554"
      },
      {
        "id": 3,
        "title": "Yellow",
        "is_correct": false,
        "created": "2020-08-31T18:53:09.561816",
        "updated": "2020-08-31T18:53:09.561838"
      }
    ]
}
```

> Create answer example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "title": "Red",
    "is_correct": true
  }
  ' \
  http://localhost:8000/api/v0/objects/poll/questions/1/answers/
```

Use poll question answers ednpoint to get list of answers or create answers.

## Test results

```shell
curl -X GET http://localhost:8000/api/v0/objects/poll/results/

```

> Success response:

```shell
[
    {
        "id": 1,
        "test": 1,
        "user": 1,
        "questions_count": 3,
        "correct_answers": 1,
        "result": 33.33,
        "created": "2020-08-31T18:56:51.486344",
        "success": false
    },
    {
        "id": 2,
        "test": 1,
        "user": 1,
        "questions_count": 3,
        "correct_answers": 2,
        "result": 66.66,
        "created": "2020-08-31T18:58:03.667621",
        "success": false
    },
    {
        "id": 3,
        "test": 1,
        "user": 1,
        "questions_count": 3,
        "correct_answers": 3,
        "result": 100.0,
        "created": "2020-08-31T19:03:11.216353",
        "success": true
    }
]
```

> Data structure for user answers:

```shell
{
  "<question ID>": "<answer_ID>",
  "<question ID>": ["<answer_ID>", "<answer_ID>"] <- multiple answers
}
```

> Send test result to API example:

```shell
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
    "1": "4",
    "2": "5",
    "3": ["12", "13"]
  }
  ' \
  http://localhost:8000/api/v0/objects/test/results/
```

Use poll results endpoint to send poll result.

**Notes:**

* If poll is not available for user, endpoint will return 403 for POST request.







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
          "place": "Kyiv",
          "certificate_template": 1,
          "start_date": "2019-09-15T14:00:00",
          "end_date": "2019-09-15T15:00:00",
          "testing_end_date": "2019-09-15T16:00:00",
          "is_draft": false,
          "image": "http://example.com/...png",
          "text": "Full event text",
          "youtube_id": "www3ssasa",
          "private": true,
          "success_percent": 70.0,
          "max_tries": 0
        },
        {
          "id": 2,
          "name": "Event in Lviv and Ternopil",
          "description": "Event description",
          "place": "Lviv, Ternopil",
          "certificate_template": 2,
          "start_date": "2019-09-15T14:00:00",
          "end_date": "2019-09-15T15:00:00",
          "testing_end_date": "2019-09-15T16:00:00",
          "is_draft": false,
          "image": "http://example.com/...png",
          "text": "Full event text",
          "youtube_id": "www2ddase",
          "private": true,
          "success_percent": 80.0,
          "max_tries": 2
        }
    ]
}
```

Endpoint | Methods | Description
-------------- | -------------- | --------------
/events/ | GET, POST | Events list endpoint.
/events/{ID}/ | GET, PUT, PATCH | Event details.
/events/{ID}/register/ | POST | Event registration endpoint.
/events/{ID}/register/check/ | GET | Event check registration endpoint.
/events/{ID}/test/ | GET, POST | Event test instance details.
/events/{ID}/test/results/ | GET, POST | Event test results list.
/events/{ID}/test/results/certificate/ | GET | Event test certificate.
/events/{ID}/test/questions/ | GET, POST | Test questions list.
/events/{ID}/test/questions/{ID}/ | GET, PUT, PATCH | Test question details.
/events/{ID}/test/questions/{ID}/answers/ | GET, POST | Test question answers list.
/events/{ID}/test/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Test question answers detail.


## Data

Field name | Type | Is required | Read only | Default value | Ordering | Filtering | Searching
-------------- | -------------- | -------------- | -------------- | -------------- | -------------- | -------------- | --------------
name | string | yes | no | | + | + | + |
description | string | yes | no | | + | | + |
text | string | yes | no | | + | | + |
place | string | no | no | | + | + | + |
certificate_template | int (ID) | no | no | |
image | file / image url | no | no | |
event_plan | file / file url | no | no | |
start_date | datetime | yes | no | | + | + |
end_date | datetime | yes | no | | + | + | 
testing_end_date | datetime | yes | no | | + | + | |
is_draft | boolean | yes | no | true | + | + | |
private | boolean | yes | no | false | + | + | | 
youtube_id | string | no | no | | + | + | + |
success_percent | float | yes | no | 70.0 | + | + | |
max_tries | int | yes | no | 0 | + | + | |
created | datetime | yes | yes | | + | + | |
updated | datetime | yes | yes | | + | + | |


## Event Registration

> Event registration example. If user is successfully registered will be returned code 200.

```shell
curl -X POST http://localhost:8000/api/v0/events/1/register/

```

> Check registration example:

```shell
curl -X GET http://localhost:8000/api/v0/events/1/register/check/

```

If event is **private** (**private=true**) user do not have access to youtube video before he registers.

To get access to the youtube video user should do registration using event registration endpoint. If user is already registered or event is no private you will recieve code 400.

For anonymous users the platform will remember client by their IP address.

You can check users registration using event check registration endpoint. If uses is registered you will recieve code 200, esle if user is not registered or event is not private you will recieve code 404.


# Webinars

## Endpoints

> Get webinars list example:

```shell
{
    "count": 1,
    "next": null,
    "previous": null,
    "results": [
        {
          "id": 1,
          "name": "How to become a doctor",
          "description": "Webinar description",
          "certificate_template": 1,
          "start_date": "2019-09-15T14:00:00",
          "end_date": "2019-09-15T15:00:00",
          "testing_end_date": "2019-09-15T16:00:00",
          "is_draft": false,
          "image": "http://example.com/...png",
          "text": "Full text",
          "private": true,
          "youtube_id": "www3ssasa",
          "success_percent": 80.0,
          "max_tries": 2
          
        }
    ]
}
```

Endpoint | Methods | Description
-------------- | -------------- | --------------
/webinars/ | GET, POST | Webinars list endpoint.
/webinars/{ID}/ | GET, PUT, PATCH | Webinar details.
/webinars/{ID}/register/ | POST | Webinar registration endpoint.
/webinars/{ID}/register/check/ | GET | Webinar check registration endpoint.
/webinars/{ID}/test/ | GET, POST | Webinar test instance details.
/webinars/{ID}/test/results/ | GET, POST | Webinar test results list.
/webinars/{ID}/test/results/certificate/ | GET | Webinar test certificate.
/webinars/{ID}/test/questions/ | GET, POST | Test questions list.
/webinars/{ID}/test/questions/{ID}/ | GET, PUT, PATCH | Test question details.
/webinars/{ID}/test/questions/{ID}/answers/ | GET, POST | Test question answers list.
/webinars/{ID}/test/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Test question answers detail.
/webinars/{ID}/poll/ | GET, POST | Webinar poll details.
/webinars/{ID}/poll/results/ | GET, POST | Webinar poll results list.
/webinars/{ID}/poll/questions/ | GET, POST | Poll questions list.
/webinars/{ID}/poll/questions/{ID}/ | GET, PUT, PATCH | Poll question details.
/webinars/{ID}/poll/questions/{ID}/answers/ | GET, POST | Poll question answers list.
/webinars/{ID}/poll/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Poll question answers detail.
/webinars/{ID}/realtime_poll/ | GET, POST | Realtime poll details.
/webinars/{ID}/realtime_poll/results/ | GET, POST | Realtime poll results list.
/webinars/{ID}/realtime_poll/questions/ | GET, POST | Realtime poll questions list.
/webinars/{ID}/realtime_poll/questions/{ID}/ | GET, PUT, PATCH | Realtime poll question details.
/webinars/{ID}/realtime_poll/questions/{ID}/launch/ | POST | Realtime poll question launch endpoint.
/webinars/{ID}/realtime_poll/questions/{ID}/answers/ | GET, POST | Realtime poll question answers list.
/webinars/{ID}/realtime_poll/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Realtime poll question answers detail.


## Data

Field name | Type | Is required | Read only | Default value | Ordering | Filtering | Searching
-------------- | -------------- | -------------- | -------------- | -------------- | -------------- | -------------- | --------------
name | string | yes | no | | + | + | + |
description | string | yes | no | | + | | + |
text | string | yes | no | | + | | + |
certificate_template | int (ID) | no | no | |
image | file / image url | no | no | |
event_plan | file / file url | no | no | |
start_date | datetime | yes | no | | + | + |
end_date | datetime | yes | no | | + | + | 
testing_end_date | datetime | yes | no | | + | + | |
is_draft | boolean | yes | no | true | + | + | |
private | boolean | yes | no | false | + | + | | 
youtube_id | string | no | no | | + | + | + |
success_percent | float | yes | no | 70.0 | + | + | |
max_tries | int | yes | no | 0 | + | + | |
created | datetime | yes | yes | | + | + | |
updated | datetime | yes | yes | | + | + | |


## Webinar Registration

> Webinar registration example. If user is successfully registered will be returned code 200.

```shell
curl -X POST http://localhost:8000/api/v0/webinars/1/register/

```

> Check registration example:

```shell
curl -X GET http://localhost:8000/api/v0/webinars/1/register/check/

```

If webinar is **private** (**private=true**) user do not have access to youtube video before he registers.

To get access to the youtube video user should do registration using event registration endpoint. If user is already registered or webinar is no private you will recieve code 400.

For anonymous users the platform will remember client by their IP address.

You can check users registration using webinar check registration endpoint. If uses is registered you will recieve code 200, esle if user is not registered or event is not private you will recieve code 404.



# Realtime Polls

> Message with poll data from server example:

```shell
{
    "webinar_id": 1, 
    "question": {
         "id": 1, 
         "title": "Question", 
         "multiple_answers": false, 
         "answers_set": [
               {"id": 1, "title": "Answer 1"},
               {"id": 2, "title": "Answer 2"}
          ]
    }
}
```

Realtime polls are the same objects like Polls.
Realtime - means that we can launch realtime question when user watching webinar. 

To launch poll send POST request to realtime poll question launch endpoint: [/webinars/{ID}/realtime_poll/questions/{ID}/launch/](/webinars/{ID}/realtime_poll/questions/{ID}/launch/)

When we launch poll all client connected to realtime polls service via WebSocket will reveive message with poll data json.

Realtime polls service (Sandbox) URL - [https://sandbox-polls.hippocrates.org.ua/](https://sandbox-polls.hippocrates.org.ua/)<br/>
WebSocket URL - [wss://sandbox-polls.hippocrates.org.ua/ws/{webinar_id}](wss://sandbox-polls.hippocrates.org.ua/ws/{webinar_id})<br/>


# Courses

## Endpoints

> Get courses list example:

```shell
{
    "count": 1,
    "next": null,
    "previous": null,
    "results": [
        {
          "id": 1,
            "name": "Surgery course",
            "description": "Complex Surgery course",
            "certificate_template": 2,
            "points": 1,
            "author": "Bill Gates",
            "specializations": [],
            "start_date": "2020-09-09",
            "end_date": "2020-09-10",
            "is_draft": true,
            "certificate_available": true,
            "course_image": "http://example.com/...png",
            "text": "Lorem ipsum dolor...",
            "success_percent": 90.0,
            "max_tries": 2,
            "has_test": true,
            "test_questions_count": 10,
            "add_to_favourites": false,
            "created": "2020-09-16T22:42:43.551044",
            "updated": "2020-09-16T22:43:08.523486",
            "lessons_set": [...],
            "course_test": {...}
        }
    ]
}
```

Endpoint | Methods | Description
-------------- | -------------- | --------------
/courses/ | GET, POST | COurse list endpoint.
/courses/{ID}/ | GET, PUT, PATCH | Course details.
/courses/{ID}/start/ | POST | Course start.
/courses/{ID}/finish/ | POST | Course finish.
/courses/{ID}/test/ | GET, POST | Course test instance details.
/courses/{ID}/test/results/ | GET, POST | Course test results list.
/courses/{ID}/test/results/certificate/ | GET | Course test certificate.
/courses/{ID}/test/questions/ | GET, POST | Test questions list.
/courses/{ID}/test/questions/{ID}/ | GET, PUT, PATCH | Test question details.
/courses/{ID}/test/questions/{ID}/answers/ | GET, POST | Test question answers list.
/courses/{ID}/test/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Test question answers detail.
/courses/{ID}/lessons/ | GET, POST | Course lessons list endpoint.
/courses/{ID}/lessons/{ID}/ | GET, PUT, PATCH | Course lesson details.
/courses/{ID}/lessons/finish/ | POST | Course lessons start.
/courses/{ID}/lessons/{ID}/files/ | GET, PUT, PATCH | Lesson attached files list.
/courses/{ID}/lessons/{ID}/files/{ID}/ | GET, PUT, PATCH | Lesson attached file detail.
/courses/{ID}/lessons/{ID}/test/ | GET, POST | Lesson test instance details.
/courses/{ID}/lessons/{ID}/start/ | POST | Lesson test start.
/courses/{ID}/lessons/{ID}/test/results/ | GET, POST | Lesson test results list.
/courses/{ID}/lessons/{ID}/test/questions/ | GET, POST | Test questions list.
/courses/{ID}/lessons/{ID}/test/questions/{ID}/ | GET, PUT, PATCH | Test question details.
/courses/{ID}/lessons/{ID}/test/questions/{ID}/answers/ | GET, POST | Test question answers list.
/courses/{ID}/lessons/{ID}/test/questions/{ID}/answers/{ID}/ | GET, PUT, PATCH | Test question answers detail.

## Courses passing

The user can pass the course as many times as specified in the **max_tries** field in course. 0 value means that course can be passed unlimited times.

To start course, you must send POST request to course start endpoint - **/courses/{ID}/start/**.
You will receive **current lesson ID**  in the success response.

Next step it's to pass lesson test if it exists. You can use **has_test** field in lesson to check that test exists.

If lesson **test_time** fields value greater then **0** we should start test before it passing (0 value means that test time is unlimited.).</br>
To start lesson test use start lesson test endpoint - **/courses/{ID}/lesson/{ID}/test/start/**. You will receive **test_end_datetime** in success response.

After test passing, you can finish current lesson and get **next lesson ID** if exists. </br>
To finish lesson use lesson finish endpoint - **/courses/{ID}/lesson/{ID}/finish/**.

When last lesson is passed you can pass course test if exists. Use the same **has_test** value as in lesson to check that test exists.

To finish course use course finish endpoint - **/courses/{ID}/start/**.

### Additional

Also you can get current course progress or lesson progress by sending POST request to following endpoints:

* **/courses/{ID}/progress/**
* **/courses/{ID}/lesson/{ID}/progress/**



# Subscriptions

Subscriptions are used to collect email addresses that can be used for mailing.

## Endpoints

```shell
curl -X GET http://localhost:8000/api/v0/subscriptions/

```

```shell
{
    "count": 1,
    "next": null,
    "previous": null,
    "results": [
        {
            "id": 1,
            "email": "codexe.pro@gmail.com",
            "created": "2020-09-03T15:03:35.846496"
        }
    ]
}

```

Endpoint | Methods | Description
-------------- | -------------- | --------------
/subscriptions/ | GET, POST | Subscriptions list endpoint.
/subscriptions/{ID}/ | GET, PUT, PATCH | Subscription details.

## Data

Field name | Type | Is required | Read only | Default value | Ordering | Filtering | Searching
-------------- | -------------- | -------------- | -------------- | -------------- | -------------- | -------------- | --------------
email | string | yes | no | | + | + | + |
created | datetime | yes | yes | | + | + | |


# Content

## Hippocrates Website Content

### Endpoints

Endpoint | Methods | Description
-------------- | -------------- | --------------
/content/hippocrates/main/ | GET, PUT, PATCH | Main information.
/content/hippocrates/home/ | GET, PUT, PATCH | Home page content.
/content/hippocrates/projects/ | GET, PUT, PATCH | Projects page content.
/content/hippocrates/contacts/ | GET, PUT, PATCH | Contacts page content.