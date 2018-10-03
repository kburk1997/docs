# Yacs API v6

## Welcome

Welcome to the Yacs API documentation!

The Yacs API fully implements [JSON-API v1](http://jsonapi.org/), a specification for creating RESTful APIs in JSON.
It's basically GraphQL on JSON.
This API is built with [Graphiti](https://graphiti-api.github.io/graphiti/), a very powerful tool for creating JSON-API compliant APIs in ruby.
We suggest you check out the JSON-API specification if you are not familiar.
If you are familiar with JSON-API, you already know how to use the Yacs API :)

The Yacs API provides several resources: **schools**, **subjects**, **courses**, **listings**, **sections**, and **terms**.
Additional resources, **instructors**, **schedules**, **clubs**, and **events** are coming soon.
Because Yacs uses JSON-API, each resource can be queried the same ways.
A consumer of the API can also easily query for associated resources, filter by any attribute, paginate, sort, and perform several other advanced operations.
Yacs has HATEOAS in its veins.

## Querying

All resources support the following query parameters.
These parameters can be combined in any way, making it extremely easy to perform complex queries.

#### **[Sort](http://jsonapi.org/format/#fetching-sorting)**

- By name, ascending:
    - `GET /api/v6/listings?sort=longname`
- By name, descending:
    - `GET /api/v6/listings?sort=-longname`

#### **[Paginate](http://jsonapi.org/format/#fetching-pagination)**

- 2 Per page:
    - `GET /api/v6/listings?page[size]=2`
- 2 Per page, second page:
    - `GET /api/v6/listings?page[size]=2&page[number]=2`

#### **[Partial Resources](http://jsonapi.org/format/#fetching-sparse-fieldsets)**

- Only render name, not description or anything else:
    - `GET /api/v6/listings?fields[listings]=longname`

#### **[Filter](http://jsonapi.org/format/#fetching-filtering)**

- Simple:
    - `GET /api/v6/listings?filter[longname]=data structures`
- Case Sensitive:
    - `GET /api/v6/listings?filter[longname][eql]=Data Structures`
- Prefix:
    - `GET /api/v6/listings?filter[longname][prefix]=data`
- Suffix:
    - `GET /api/v6/listings?filter[longname][suffix]=structures`
- Contains:
    - `GET /api/v6/listings?filter[longname][match]=struct`
- Greater Than:
    - `GET /api/v6/listings?filter[max_credits][gt]=1`
- Greater Than or Equal To:
    - `GET /api/v6/listings?filter[max_credits][gte]=3`
- Less Than:
    - `GET /api/v6/listings?filter[max_credits][lt]=4`
- Less Than or Equal To:
    - `GET /api/v6/listings?filter[max_credits][lte]=2`

#### **[Statistics](https://graphiti-api.github.io/graphiti/quickstart)**

- Count:
    - `GET /api/v6/posts?sections[total]=count`
- More coming soon

#### **[Relationships](http://jsonapi.org/format/#fetching-relationships)**

- Include related resource (has many):
    - `GET /api/v6/listings?include=sections`
- Include related resource (belongs to):
    - `GET /api/v6/courses?include=subjects`
- Deep queries:
    - `GET /api/v6/sections?include=listings&filter[listing.longname]=Data Structures`

### Deep Queries

TODO: this section

## Resources

### Schools

### Subjects

### Courses

### Terms

### Listings

### Sections

## Group School



## Group Current User

## Session [/session]

The session resource allows users to sign in, sign out and to check their
current session status.

A user can have several open sessions at the same time.

If the session identified by the Authorization header can't be found, a
`401` error is returned (see discussion about error status codes in
https://twitter.com/gr2m/status/697804572623007744)

### Sign In [PUT]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json

    + Body

            {
                "data": {
                    "type": "session",
                    "attributes": {
                        "username": "john-doe",
                        "password": "secret"
                    }
                }
            }

+ Response 201 (application/vnd.api+json)

    + Headers

            Location: https://example.com/session

    + Body

            {
                "links": {
                    "self": "https://example.com/session"
                },
                "data": {
                    "id": "sessionid123",
                    "type": "session",
                    "relationships": {
                        "account": {
                            "links": {
                                "related": "https://example.com/session/account"
                            },
                            "data": {
                                "id": "abc1234",
                                "type": "account"
                            }
                        }
                    }
                },
                "included": [{
                    "id": "abc1234",
                    "type": "account",
                    "attributes": {
                        "username": "jane-doe"
                    },
                    "relationships": {
                        "profile": {
                            "links": {
                                "related": "https://example.com/session/account/profile"
                            },
                            "data": {
                                "id": "abc1234-profile",
                                "type": "profile"
                            }
                        }
                    }
                }]
            }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Invalid credentials"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "data.type must be 'session'"
            }]
        }

+ Response 403 (application/vnd.api+json)

        {
            errors: [{
                "status": "403",
                "title": "Forbidden",
                "detail": "Authorization header not allowed"
            }]
        }

### Check session [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/session"
            },
            "data": {
                "id": "sessionid123",
                "type": "session",
                "relationships": {
                    "account": {
                        "links": {
                            "related": "https://example.com/session/account"
                        },
                        "data": {
                            "id": "abc1234",
                            "type": "account"
                        }
                    }
                }
            },
            "included": [{
                "id": "abc1234",
                "type": "account",
                "attributes": {
                    "username": "jane-doe"
                },
                "relationships": {
                    "profile": {
                        "links": {
                            "related": "https://example.com/session/account/profile"
                        },
                        "data": {
                            "id": "abc1234-profile",
                            "type": "profile"
                        }
                    }
                }
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Sign Out [DELETE]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

## Account [/session/account]

The User account resource allows users to sign up, get
or set account properties or destroy their accounts.

### Sign Up [PUT /session/account]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json

    + Attributes (object)
        + data (object, required)
            + type (string, required)
            + attributes (object, required) may contain the     &nbsbproperties below as well as any others that - - should be in
                + username (string)
                + password (string)
                + id (string) the server may accept a - - client-generated  &nbsbid along with the r

    + Body

            {
                "data": {
                    "type": "account",
                    "id": "abc4567"
                    "attributes": {
                        "username": "john-doe",
                        "password": "secret",
                        "createdAt": "2016-10-02T04:07:20.243Z"
                    }
                }
            }

+ Response 201 (application/vnd.api+json)

    + Headers

            Location: https://example.com/session/account

    + Body

            {
                "links": {
                    "self": "https://example.com/session/account"
                },
                "data": {
                    "id": "abc4567",
                    "type": "account",
                    "attributes": {
                        "username": "john-doe"
                    },
                    "relationships": {
                        "profile": {
                            "links": {
                                "related": "https://example.com/session/account/profile"
                            },
                            "data": {
                                "id": "abc4567-profile",
                                "type": "profile"
                            }
                        }
                    }
                }
            }

+ Response 403 (application/vnd.api+json)

        {
            errors: [{
                "status": "403",
                "title": "Forbidden",
                "detail": "Authorization header not allowed"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "An account with that username already exists"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "data.type must be 'account'"
            }]
        }

### Fetch [GET /session/account]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/session/account"
            },
            "data": {
                "id": "abc4567",
                "type": "account",
                "attributes": {
                    "username": "john-doe"
                },
                "relationships": {
                    "profile": {
                        "links": {
                            "related": "https://example.com/session/account/profile"
                        },
                        "data": {
                            "id": "abc4567-profile",
                            "type": "profile"
                        }
                    }
                }
            }
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Update [PATCH]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

    + Body

            {
                "data": {
                    "id": "def678",
                    "type": "account",
                    "attributes": {
                        "password": "newsecret"
                    }
                }
            }

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "data.type must be 'account'"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "data.id must be '{id}'"
            }]
        }

### Close [DELETE]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }


## Profile [/session/account/profile]

All custom user data is stored in the profile.

Every account has an associated profile, by default, an empty one
is created when creating the account.

### Fetch [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/session/account/profile"
            },
            "data": {
                "id": "abc123-profile",
                "type": "profile",
                "attributes": {
                    "fullName": "Jane Doe"
                }
            }
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Update [PATCH]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

    + Body

            {
                "data": {
                    "id": "abc123-profile",
                    "type": "profile",
                    "attributes": {
                        "property": "new value"
                    }
                }
            }

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "'type' and 'id' provided don't match any existing document"
            }]
        }

## Group Requests


Requests can be used for things like account confirmation, password resets
or account ugrades, which require app-specific workflows.

Authentication might or might not be needed, depending on the type of request.

## Request Collection [/requests]

A user can only see own requests. An admin can see all requests across accounts.

### List all [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/requests",
                "first": "https://example.com/requests",
                "last": "https://example.com/requests?page=2",
                "next": "https://example.com/requests?page=2"
            },
            "data": [
                {
                    "id": "1234",
                    "type": "request",
                    "links": {
                        "self": "https://example.com/requests/1234"
                    },
                    "attributes": {
                        "type": "passwordreset",
                        "email": "john@example.com",
                        "expires": "2015-07-20T10:00:00-04:00"
                    }
                },
                {
                    "id": "2345",
                    "type": "request",
                    "links": {
                      "self": "https://example.com/requests/2345"
                    },
                    "attributes": {
                        "type": "usernamereminder",
                        "email": "jane@example.com",
                        "expires": "2015-07-20T15:00:00-04:00"
                    }
                }
            ]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Create [POST]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

    + Body

            {
                "data": {
                    "type": "request",
                    "attributes": {
                        "type": "passwordreset",
                        "email": "john@example.com"
                    }
                }
            }

+ Response 201 (application/vnd.api+json)

    + Headers

            Location: https://example.com/requests/1234

    + Body

            {
                "links": {
                    "self": "https://example.com/requests/1234"
                },
                "data": {
                    "id": "1234",
                    "type": "request",
                    "attributes": {
                        "type": "passwordreset",
                        "email": "john@example.com",
                        "expires": "2015-07-20T15:00:00-04:00"
                    }
                }
            }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "data.type must be 'request'"
            }]
        }

+ Response 403 (application/vnd.api+json)

        {
            errors: [{
                "status": "403",
                "title": "Forbidden",
                "detail": "Unknown request type"
            }]
        }


## Request [/requests/{id}]

`DELETE` always requires authentication and is restricted to admins.

`GET` makes sense for users to check for errors that might have occured after
creating a request, like an error during password reset delivery. It might or
might not require authentication, depending on the type of request.

+ Parameters
    - + id: 1234 (required, string) request id
    - 
### Fetch [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/requests/1234"
            },
            "data": {
                "id": "1234",
                "type": "request",
                "attributes": {
                    "type": "passwordreset",
                    "email": "john@example.com",
                    "expires": "2015-07-20T15:00:00-04:00"
                }
            }
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Delete [DELETE]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

## Group Admins


## User Account Collection [/accounts]

Admins can fetch and manage all user accounts and their profile properties.

### Create [POST]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

    + Body

            {
                "data": {
                    "type": "account",
                    "attributes": {
                        "username": "jane-doe",
                        "password": "secret"
                    }
                }
            }

+ Response 201 (application/vnd.api+json)

    + Headers

            Location: https://example.com/accounts/abc4567

    + Body

            {
                "links": {
                    "self": "https://example.com/accounts/abc4567"
                },
                "data": {
                    "id": "abc4567",
                    "type": "account",
                    "attributes": {
                        "username": "jane-doe"
                    },
                    "relationships": {
                        "profile": {
                            "links": {
                                "related": "https://example.com/accounts/abc4567/profile"
                            },
                            "data": {
                                "id": "abc4567-profile",
                                "type": "profile",
                            }
                        }
                    }
                }
            }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "data.type must be 'account'"
            }]
        }

### List all [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/accounts",
                "first": "https://example.com/accounts",
                "last": "https://example.com/accounts?page=2",
                "next": "https://example.com/accounts?page=2"
            },
            "data": [
                {
                    "id": "abc4567",
                    "type": "account",
                    "links": {
                        "self": "https://example.com/accounts/abc4567"
                    },
                    "attributes": {
                        "username": "jane-doe"
                    },
                    "relationships": {
                        "profile": {
                            "links": {
                                "related": "https://example.com/accounts/abc4567/profile"
                            },
                            "data": {
                                "id": "abc4567-profile",
                                "type": "profile"
                            }
                        }
                    }
                },
                {
                    "id": "def678",
                    "type": "account",
                    "links": {
                        "self": "https://example.com/accounts/def678"
                    },
                    "attributes": {
                        "username": "john-doe"
                    },
                    "relationships": {
                        "profile": {
                            "links": {
                                "related": "https://example.com/accounts/def678/profile"
                            },
                            "data": {
                                "id": "def678-profile",
                                "type": "profile"
                            }
                        }
                    }
                }
            ]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

## User Account [/accounts/{id}]

+ Parameters

    - + id: abc4567 (required, string) id of account
    - 
### Fetch [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/accounts/def678"
            },
            "data": {
                "id": "def678",
                "type": "account",
                "attributes": {
                    "username": "jane-doe",
                }
            },
            "relationships": {
                "profile": {
                    "links": {
                        "related": "https://example.com/accounts/def678/profile"
                    },
                    "data": {
                        "id": "def678-profile",
                        "type": "profile"
                    }
                }
            }
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Update [PATCH]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

    + Body

            {
                "data": {
                    "id": "abc1234",
                    "type": "account",
                    "attributes": {
                        "newProperty": "value",
                        "secureProperty": "wicked"
                    }
                }
            }

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "'type' and 'id' provided don't match any existing document"
            }]
        }

### Delete [DELETE]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

## User Profile [/accounts/{id}/profile]

All custom user data is stored in the profile.

+ Parameters

    - + id: abc4567 (required, string) id of account
    - 
### Fetch [GET]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 200 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/accounts/abcd123/profile"
            },
            "data": {
                "id": "abcd123-profile",
                "type": "profile",
                "attributes": {
                    "fullName": "Jane Doe"
                }
            }
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

### Update [PATCH]

+ Request (application/vnd.api+json)

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

    + Body

            {
                "data": {
                    "id": "abcd123-profile",
                    "type": "profile",
                    "attributes": {
                        "property": "new value"
                    }
                }
            }

+ Response 204

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }

+ Response 409 (application/vnd.api+json)

        {
            errors: [{
                "status": "409",
                "title": "Conflict",
                "detail": "'type' and 'id' provided don't match any existing document"
            }]
        }

## User Session [/accounts/{id}/sessions]

Admins can manage sessions for all user accounts

+ Parameters

    - + id: abc4567 (required, string) id of account
    - 
### Create [POST]

+ Request

    + Headers

            Accept: application/vnd.api+json
            Authorization: Bearer sessionid123

+ Response 201 (application/vnd.api+json)

        {
            "links": {
                "self": "https://example.com/accounts/abcd123/sessions/session123"
            },
            "data": {
                "id": "session123",
                "type": "session",
                "relationships": {
                    "account": {
                        "links": {
                            "related": "https://example.com/accounts/abcd123"
                        },
                        "data": {
                            "id": "abcd123",
                            "type": "account"
                        }
                    }
                }   
            }
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Authorization header missing"
            }]
        }

+ Response 401 (application/vnd.api+json)

        {
            errors: [{
                "status": "401",
                "title": "Unauthorized",
                "detail": "Session invalid"
            }]
        }