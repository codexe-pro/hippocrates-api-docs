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
/api/v0/auth/token/ | POST | Accepts the user's email and password and returns access and refresh tokens.
/api/v0/token/refresh/ | POST | When short-lived access token expires, you can use the longer-lived refresh token to obtain another access token.

# Objects

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
  -X POST \
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
/api/v0/objects/ | GET, POST | This endpoint uses to get list of objects (using GET method) and to create new objects (using POST method).
/api/v0/objects/{ID}/ | GET, PUT, PATCH, DELETE | Uses to get specific object by ID (GET), update object (PUT), partial update (PATCH) and delete the object (DELETE).

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

# Specializations

Specializations objects describe doctors specialization and related with User objects.<br />

## Endpoints

Endpoint | Methods | Description
-------------- | -------------- | --------------
/api/v0/specializations/ | GET, POST | Specializations list endpoint.
/api/v0/specializations/{ID}/ | GET, PUT, PATCH, DELETE | Specific Specialization detail endpoint.

## Data

Field name | Type | Is required | Read only | Default value
-------------- | -------------- | -------------- | -------------- | --------------
name | string | yes | no
secondary_name | string | yes | no
banner | file / image url | no | no
main | boolean | yes | no | false
main_on_categories_page | bool | yes | no | false
sort_id | int | yes | no
created | datetime | yes | yes
updated | datetime | yes | yes

# Users

## Endpoints

Endpoint | Methods | Description
-------------- | -------------- | --------------
/api/v0/users/register/ | POST | New users registration endpoint.
/api/v0/users/confirmation/{uid}/{token}/ | GET | Email address confirmation endpoint.<br />{uid} - users encoded ID;<br />{token} - confirmation token generated by platform.
/api/v0/users/ | GET, POST | Users list endpoint.
/api/v0/users/{ID}/ | GET, PUT, PATCH, DELETE | User detail endpoint.

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
  http://localhost:8000/api/v0/users/registration/
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

To register new user, POST user data to **/api/v0/users/registration/** endpoint.<br />
After success registration the platform will send confirmation link to user's email.<br />
When user will visit confirmation link - **email_confirmed** field value will be changed to 'true'.


## Data

Field name | Type | Is required | Read only | Description
-------------- | -------------- | -------------- | -------------- | --------------
email | string | yes | no | Users email is also username
first_name | string | yes | no
last_name | string | yes | no
parent_name | string | no | no
phone | string | yes | no
work_phone | string | no | no
birth_date | date | no | no
region | string | yes | no
town | string | yes | no
college | string | no | no
diploma | string | no | no
job_place | string | yes | no
job_name | string | yes | no
specialization | int | yes | no | [Specializations](#specializations) object ID
email_confirmed | bolean | yes  | yes

