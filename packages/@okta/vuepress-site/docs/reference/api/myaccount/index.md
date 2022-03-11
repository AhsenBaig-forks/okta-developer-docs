---
title: MyAccount
category: management
---

# MyAccount API

<ApiLifecycle access="ea" />

> **Note:** The MyAccount API is being enhanced, accessible at `/api/v1/idp/myaccount`. Use this endpoint if you plan to use the MyAccount API. The existing endpoint (`/api/v1/myaccount`) is deprecated.

The Okta MyAccount API allows end users (with or without administrator access) to fetch and update their own Okta user profiles.  It implements a subset of the existing [Users API](/docs/reference/api/users/) but with significant differences.  This API does not expose information an end user should not have access to, and it does not support lifecycle operations.

All operations in this API implicitly refer to the user making the API call.  No user ID is needed (or even accepted).

<!--
## Get started

Explore the MyAccount API:

-->

## MyAccount Operations

The MyAccount API has the following operations:

* [Get data about Me](#get-me)
* [Get my User Profile Schema](#get-my-user-profile-schema)
* [Get my User Profile](#get-my-user-profile)
* [Update my User Profile](#update-my-user-profile)
* [Challenge My Email](#challenge-my-email)
* [Verify My Email](#verify-my-email)
* [Poll My Email Challenge](#poll-my-email-challenge)


### Challenge My Email

<ApiOperation method="post" url="/idp/myaccount/emails/{id}/challenge" />

Sends a challenge to the email, user receives a 'Confirm email address change' email with an one time passcode to verify the email.
In addition, user also receives a - 'Notice of pending email address change' email. Once challenge verified, the email becomes active.

#### Required scope and role

An Okta scope of `okta.myAccount.email.manage` is required to use this endpoint.

> **Note:** Admin users are not allowed to call the `/idp/myaccount/emails/{id}/challenge` POST endpoint.

#### API Versioning
An valid API version in the `Accept` header is required to access the API. Current version V1.0.0

```json
Accept: application/json; okta-version=1.0.0
```

#### Request path parameters
| Parameter  | Type   | Description                                       |
| ---------- | ------ | ------------------------------------------------- |
| `id` | String | ID of the email. the ID of the email can be obtained through `GET /idp/myaccount/emails` or `POST /idp/myaccount/emails` when adding a new email address. |

#### Request query parameters

N/A

#### Request body
| Property | Type                     | Description                          |
| -------- | -------------------------|--------------------------------------|
| `state` | String | (Optional) The state parameter, the state of the client |

#### Response body

The requested [My Email Challenge Response object](#my-email-challenge-response-object)

#### Usage example

Any non-admin user with a valid bearer token can issue this request to challenge their emails.

##### Request

```bash
curl -v -X POST \
-H "Authorization: Bearer ${token}" \
"https://${yourOktaDomain}/idp/myaccount/emails/{id}/challenge"
```

##### Response

Returns an HTTP 201 status code response.

```json
{
    "id": "myaccount.0Up-umuhQ1adW2EGRAAK1g",
    "status": "UNVERIFIED",
    "expiresAt": "2022-03-04T21:19:43.315Z",
    "profile": {
        "email": "some.primary.email@okta.com"
    },
    "_links": {
        "verify": {
            "href": "https://jay-oie-org1.okta1.com/idp/myaccount/emails/92fc7f9445e413e94e544cc3b30483ce/challenge/myaccount.0Up-umuhQ1adW2EGRAAK1g/verify",
            "hints": {
                "allow": [
                    "POST"
                ]
            }
        },
        "poll": {
            "href": "https://jay-oie-org1.okta1.com/idp/myaccount/emails/92fc7f9445e413e94e544cc3b30483ce/challenge/myaccount.0Up-umuhQ1adW2EGRAAK1g",
            "hints": {
                "allow": [
                    "GET"
                ]
            }
        }
    }
}

```

### Verify My Email

<ApiOperation method="post" url="/idp/myaccount/emails/{id}/challenge/{challengeId}/verify" />

Verify the email challenge with the verification code that the user receives from the 'Confirm email address change' email. The email is active upon a successful verification.

#### Required scope and role

An Okta scope of `okta.myAccount.email.manage` is required to use this endpoint.

> **Note:** Admin users are not allowed to call the `/idp/myaccount/emails/{id}/challenge/{challengeId}/verify` POST endpoint.

#### API Versioning
An valid API version in the `Accept` header is required to access the API. Current version V1.0.0

```json
Accept: application/json; okta-version=1.0.0
```

#### Request path parameters
| Parameter  | Type   | Description                                       |
| ---------- | ------ | ------------------------------------------------- |
| `id` | String | ID of the email. the ID of the email can be obtained through `GET /idp/myaccount/emails` or `POST /idp/myaccount/emails` when adding a new email address. |
| `challengeId` | String | The challengeId of the email. the challengeId can be obtained through `POST /idp/myaccount/emails/{id}/challenge/` when creating a new challenge. |

#### Request query parameters

N/A

#### Request body

This request requires the `verificationCode` property as its request body.

| Property | Type                     | Description                          |
| -------- | -------------------------|--------------------------------------|
| `verificationCode`  | String | A 6-digit verification code that the user receives from the 'Confirm email address change' email |

#### Response body

#### Usage example

Any non-admin user with a valid bearer token can issue this request to verify their emails.

##### Request

```bash
curl -v -X POST \
-H "Authorization: Bearer ${token}" \
"https://${yourOktaDomain}/idp/myaccount/emails/{id}/challenge/{challengeId}/verify"
```

##### Response
Returns an empty HTTP 204 status code response.

### Poll My Email Challenge

<ApiOperation method="get" url="/idp/myaccount/emails/{id}/challenge/{challengeId}" />

Fetches the email challenge's status

#### Required scope and role

An Okta scope of `okta.myAccount.email.read` is required to use this endpoint.

#### API Versioning
An valid API version in the `Accept` header is required to access the API. Current version V1.0.0

```json
Accept: application/json; okta-version=1.0.0
```

#### Request path parameters
| Parameter  | Type   | Description                                       |
| ---------- | ------ | ------------------------------------------------- |
| `id` | String | ID of the email. the ID of the email can be obtained through `GET /idp/myaccount/emails` or `POST /idp/myaccount/emails` when adding a new email address. |
| `challengeId` | String | The challengeId of the email. the challengeId can be obtained through `POST /idp/myaccount/emails/{id}/challenge/` when creating a new challenge. |

#### Request query parameters

N/A

#### Response body

The requested [My Email Challenge object](#my-email-challenge-object)

#### Usage example

Any non-admin user with a valid bearer token can issue this request to get their email challenge status.

##### Request

```bash
curl -v -X GET \
-H "Authorization: Bearer ${token}" \
"https://${yourOktaDomain}/idp/myaccount/emails/{id}/challenge/{challengeId}"
```

##### Response

```json
{
    "id": "myaccount.y-YPC4HiTbGEAeEwmHI2MA",
    "status": "UNVERIFIED",
    "expiresAt": "2022-03-04T17:29:57.067Z",
    "profile": {
        "email": "some.primary.email@okta.com"
    }
}
```

### Get Me

<ApiOperation method="get" url="/api/v1/myaccount" />
<ApiLifecycle access="deprecated" />

Fetches the current user's Me object, a collection of links to information describing the user.

#### Request path parameters

N/A

#### Request query parameters

N/A

#### Response body

The requested [Me object](#me-object)

#### Usage example

Any user with a valid session can issue this request to get basic information about their account.

##### Request

```bash
curl -v -X GET \
-H "Authorization: SSWS ${api_token}" \
"https://${yourOktaDomain}/api/v1/myaccount"
```

##### Response

```json
{
    "_links": {
        "directoryProfile": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/directoryProfile"
        },
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "createdAt": "2020-01-14T20:05:32.000Z",
    "id": "00u21l3rOYRXX1tnI0g4",
    "modifiedAt": "2020-10-13T03:17:09.000Z"
}
```


### Get My User Profile Schema

<ApiOperation method="get" url="/api/v1/myaccount/profile/schema" />

Fetches the appropriate User Profile Schema for the caller's [User Type](/docs/reference/api/user-types/).

> **Note:** If a property's value is not visible to an end user (because it is hidden or [sensitive](https://help.okta.com/okta_help.htm?id=ext-hide-sensitive-attributes) then the property's definition will also be hidden in the output of this API.

#### Request path parameters

N/A

#### Request query parameters

N/A

#### Response body

The [User Profile Schema](#user-profile-schema-object) for the caller.

#### Usage example

Any user with a valid session can issue this request to get the Schema for their User Profile.

##### Request

```bash
curl -v -X GET \
-H "Authorization: SSWS ${api_token}" \
"https://${yourOktaDomain}/api/v1/myaccount/profile/schema"
```

##### Response

```json
{
    "_links": {
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/profile/schema"
        },
        "user": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "properties": {
        "customBoolean": {
            "permissions": {
                "SELF": "READ_WRITE"
            },
            "title": "customBoolean",
            "type": "boolean"
        },
        "foo": {
            "permissions": {
                "SELF": "READ_ONLY"
            },
            "title": "foo",
            "type": "string"
        },
        "login": {
            "maxLength": 100,
            "minLength": 5,
            "permissions": {
                "SELF": "READ_ONLY"
            },
            "required": true,
            "title": "Username",
            "type": "string"
        },
        "mobilePhone": {
            "maxLength": 100,
            "permissions": {
                "SELF": "READ_WRITE"
            },
            "title": "Mobile phone",
            "type": "string"
        },
        "customInteger": {
            "permissions": {
                "SELF": "READ_WRITE"
            },
            "title": "customInteger",
            "type": "integer"
        }
    }
}
```

### Get My User Profile

<ApiOperation method="get" url="/api/v1/myaccount/directoryProfile" />

Fetches the caller's Okta User Profile, excluding any attribute also excluded by [Get My User Profile Schema](#get-my-user-profile-schema)


#### Request query parameters

| Parameter | Type        | Description                                                                                            |
| --------- | ----------- | ------------------------------------------------------------------------------------------------------ |
| `expand`  | String      | (Optional) If specified as `schema`, the User Profile Schema is included in the `embedded` attribute. |


#### Response body

Returns a [User Profile](#user-profile-object).

#### Usage example

Any user with a valid session can issue this request to get their User Profile.

##### Request

This request would retriever the requesting User's Profile.

```bash
curl -v -X GET \
-H "Authorization: SSWS ${api_token}" \
"https://${yourOktaDomain}/api/v1/myaccount/directoryProfile"
```

##### Response

```json
{
    "_links": {
        "describedBy": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/profile/schema"
        },
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/directoryProfile"
        },
        "user": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "createdAt": "2020-01-14T20:05:32.000Z",
    "modifiedAt": "2020-10-13T03:17:09.000Z",
    "profile": {
        "customBoolean": null,
        "foo": "bar",
        "login": "example@ex.ample.com",
        "mobilePhone": null,
        "customInteger": null
    }
}
```


### Update My User Profile

<ApiOperation method="put" url="/api/v1/myaccount/directoryProfile" />

Updates the caller's User Profile.

> **Note:** This API differs from the the existing [Users API](/docs/reference/api/users/) in that only PUT is supported.  This API also does not support partial update.  All values returned from fetching User Profile must be passed to this API, or the update will not pass validation.  This applies even if the omitted schema property is optional. To unset an optional property, explicitly pass the property with a value of `null`.

#### Request path parameters

N/A

#### Request query parameters

N/A

#### Request body

This API requires the `profile` property of a [User Profile](#user-profile-object) as its request body.

| Property | Type                     | Description                          |
| -------- | -------------------------|--------------------------------------|
| `profile`  | Object | The properties defined in the schema |

#### Response body

Returns the result of applying the update, as if the caller had invoked the GET User Profile operation.

#### Usage example

##### Request

This request would update the user profile of the caller to have exactly the values specified.

```bash
curl -XPUT 'https://${yourOktaDomain}/api/v1/myaccount/directoryProfile' -H 'Authorization: SSWS {token}' -H 'Content-Type: application/json' --data '{
     "profile": {
         "customBoolean": false,
         "foo": "bar",
         "login": "dayton.williams@okta.com",
         "notFive": 5,
         "customInteger": null
     }
 }'
```

##### Response

```json
{
    "_links": {
        "describedBy": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/profile/schema"
        },
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/directoryProfile"
        },
        "user": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "createdAt": "2020-01-14T20:05:32.000Z",
    "modifiedAt": "2020-11-03T06:01:13.000Z",
    "profile": {
        "customBoolean": false,
        "foo": "bar",
        "login": "example@ex.ample.com",
        "mobilePhone": null,
        "customInteger": 5
    }
}
```

### Send Phone Challenge

<ApiOperation method="post" url="/idp/myaccount/phones/{id}/challenge"/>

Sends a phone challenge using one of two methods: `SMS` or `CALL`. This request can also handle a resend challenge (retry).

Upon a successful challenge, the user receives a verification code by `SMS` or `CALL`. Send a `POST` request to the `/idp/myaccount/phones/{id}/verify` endpoint to use the verification code to verify the phone number. The verification code expires in 5 minutes.

> **Note:** Sending requests to the `/idp/myaccount/phones/{id}/challenge` endpoint more often than once very 30 seconds, or at a rate that exceeds the rate limit rule configured by the admin, returns a 429 (Too Many Requests) error.

#### Required scope and role

An Okta scope of `okta.myAccount.phone.manage` is required to use this endpoint.

> **Note:** Admin users are not allowed to call the `/idp/myaccount/phones/{id}/challenge` endpoint.

#### Required scope and role

#### Request path parameters

| Parameter  | Type   | Description                                       |
| ---------- | ------ | ------------------------------------------------- |
| `id` | String | The id of the phone factor. Found in the response when a new phone number is created successfully (`POST /idp/myaccount/phones`) or phone(s) is retrieved (`GET /idp/myaccount/phones`)  |

#### Request query parameters

N/A

#### Request body

This request requires the `method` property as its request body. An optional boolean `retry` property can be used to indicate resend challenge when its value is set to `true`.

| Property | Type                     | Description                          |
| -------- | -------------------------|--------------------------------------|
| `method`  | String | The method with which the challenge should be sent, valid values are `SMS` and `CALL` |
| `retry`  | Boolean | An optional property that indicates whether this is a normal challenge or retry |

#### Response body

Returns an empty response with an HTTP 200 status code.

Passing an invalid `method` returns a 400 BAD REQUEST with error code E0000001.

Disabling the factor type of the corresponding method on the org returns a 403 FORBIDDEN with error code E0000038.

Passing an invalid `id` returns a 404 NOT FOUND with error code E0000008.

Call providers failing to send the out of band OTP challenge returns 500 with error code E0000138.

#### Usage example

##### Request

This request sends a verification code by SMS to the phone number represented by `id`. The request is a normal phone challenge, not a retry.

```bash
curl -XPOST 'https://${yourOktaDomain}/myaccount/phones/{id}/challenge' -H 'Authorization: bearer {token}' -H 'Content-Type: application/json' --data '{
     "method": "SMS",
     "retry": false
 }'
```

### Verify Phone Challenge

<ApiOperation method="post" url="/idp/myaccount/phones/{id}/verify"/>

Verify the phone number with the verification code that the user receives via `SMS` or `CALL`. The phone number is active upon a successful verification.

> **Note:** Sending requests to the `/idp/myaccount/phones/{id}/verify` endpoint at a rate that exceeds the rate limit rule configured by the admin, returns a 429 (Too Many Requests) error.

#### Required scope and role

An Okta scope of `okta.myAccount.phone.manage` is required to use this endpoint.

> **Note:** Admin users are not allowed to call the `/idp/myaccount/phones/{id}/verify` endpoint.

#### Required scope and role

#### Request path parameters

| Parameter  | Type   | Description                                       |
| ---------- | ------ | ------------------------------------------------- |
| `id` | String | The id of the phone factor. Found in the response when a new phone number is created successfully (`POST /idp/myaccount/phones`) or phone(s) is retrieved (`GET /idp/myaccount/phones`)  |

#### Request query parameters

N/A

#### Request body

This request requires the `verificationCode` property as its request body.

| Property | Type                     | Description                          |
| -------- | -------------------------|--------------------------------------|
| `verificationCode`  | String | A 6-digit verification code that the user receives via `SMS` or `CALL` |

#### Response body

Returns an empty response with an HTTP 204 status code.

Disabling the factor type of the corresponding method on the org returns a 403 FORBIDDEN with error code E0000038.

Disabling the `IDP_MY_ACCOUNT_API` feature flag on the org returns a 401 UNAUTHORIZED with error code E0000015.

Passing an invalid `id` returns a 404 NOT FOUND with error code E0000008.

Passing an invalid `verificationCode` returns a 400 BAD REQUEST with error code E0000001.

Passing a valid `verificationCode` again (verifying a phone number that has been verified) results a no-op.

In an extremely rare case, verifying a phone number when the challenge is about to expire returns a 409 CONFLICT with error code E0000157.

Failing to answer the challenge (possibly due to invalid `verificationCode`) returns a 401 UNAUTHORIZED with error code E0000004.

#### Usage example

##### Request

The following request verifies the phone number represented by `id` with a `verificationCode` of 796672.

```bash
curl -XPOST 'https://${yourOktaDomain}/myaccount/phones/{id}/verify' -H 'Authorization: bearer {token}' -H 'Content-Type: application/json' --data '{
     "verificationCode": "796672"
 }'
```



## MyAccount API objects

### My Email Challenge object

#### My Email Challenge properties

| Property           | Type                                                            | Description                                                                                                       |
| ------------------ | --------------------------------------------------------------- | ------------------------------------------------------ |
| `id`               | String                                                            | The caller's email ID|
| `status`           | Object                                                            | The caller's email challenge status, VERIFIED, OR UNVERIFIED|
| `expiresAt`        | String (ISO-8601)                                                 | The time this challenge expires, the time to live of a challenge is 5 minutes|
| `profile`          | Object                                                            | The profile object defines the email address on the profile.|

#### My Email Challenge example

```json
{
    "id": "myaccount.6FLx1qb0Q8KNZSsgS150fA",
    "status": "UNVERIFIED",
    "expiresAt": "2022-03-04T17:19:03.451Z",
    "profile": {
        "email": "some.primary.email@okta.com"
    }
}
```

### My Email Challenge Response object

#### My Email Challenge Response properties

| Property           | Type                                                            | Description                                                                                                       |
| ------------------ | --------------------------------------------------------------- | ------------------------------------------------------ |
| `id`               | String                                                            | The caller's email ID|
| `status`           | Object                                                            | The caller's email challenge status, VERIFIED, OR UNVERIFIED|
| `expiresAt`        | String (ISO-8601)                                                 | The time this challenge expires, the time to live of a challenge is 5 minutes|
| `profile`          | Object                                                            | The profile object defines the email address on the profile.|
| `_links`           | Object ([JSON HAL](http://tools.ietf.org/html/draft-kelly-json-hal-06))  | Discoverable resources related to the caller's email challenge|

#### My Email Challenge Response example

```json
{
    "id": "myaccount.0Up-umuhQ1adW2EGRAAK1g",
    "status": "UNVERIFIED",
    "expiresAt": "2022-03-04T21:19:43.315Z",
    "profile": {
        "email": "some.primary.email@okta.com"
    },
    "_links": {
        "verify": {
            "href": "https://jay-oie-org1.okta1.com/idp/myaccount/emails/92fc7f9445e413e94e544cc3b30483ce/challenge/myaccount.0Up-umuhQ1adW2EGRAAK1g/verify",
            "hints": {
                "allow": [
                    "POST"
                ]
            }
        },
        "poll": {
            "href": "https://jay-oie-org1.okta1.com/idp/myaccount/emails/92fc7f9445e413e94e544cc3b30483ce/challenge/myaccount.0Up-umuhQ1adW2EGRAAK1g",
            "hints": {
                "allow": [
                    "GET"
                ]
            }
        }
    }
}
```

### Me object

#### Me properties

The Me object has several properties:

| Property           | Type                                                            | Description                                                                                                       |
| ------------------ | --------------------------------------------------------------- | ------------------------------------------------------ |
| `_links`             | Object ([JSON HAL](http://tools.ietf.org/html/draft-kelly-json-hal-06))  | Discoverable resources related to the caller's account |
| `createdAt`          | String (ISO-8601)                                                         | The timestamp the caller's account was created         |
| `id`                 | String                                                            | The caller's user ID                                   |
| `modifiedAt`         | String (ISO-8601)                                                         | The timestamp the caller's account was last updated    |


#### Me example

```json
{
    "_links": {
        "directoryProfile": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/directoryProfile"
        },
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "createdAt": "2020-01-14T20:05:32.000Z",
    "id": "00u21l3rOYRXX1tnI0g4",
    "modifiedAt": "2020-10-13T03:17:09.000Z"
}
```

### User Profile Schema object

#### User Profile Schema properties

The User Profile Schema object has several properties:

| Property           | Type                                                                                                              | Description                                                        |
| ------------------ | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `_links`             | Object ([JSON HAL](http://tools.ietf.org/html/draft-kelly-json-hal-06))                                                    | Discoverable resources related to the caller's User Profile Schema |
| `properties`         | Object | The properties defined in the [User Profile Schema](/docs/reference/api/schemas/#user-profile-schema-property-object)                               |

#### User Profile Schema example

```json
{
    "_links": {
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/profile/schema"
        },
        "user": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "properties": {
        "customBoolean": {
            "permissions": {
                "SELF": "READ_WRITE"
            },
            "title": "customBoolean",
            "type": "boolean"
        },
        "foo": {
            "permissions": {
                "SELF": "READ_ONLY"
            },
            "title": "foo",
            "type": "string"
        },
        "login": {
            "maxLength": 100,
            "minLength": 5,
            "permissions": {
                "SELF": "READ_ONLY"
            },
            "required": true,
            "title": "Username",
            "type": "string"
        },
        "mobilePhone": {
            "maxLength": 100,
            "permissions": {
                "SELF": "READ_WRITE"
            },
            "title": "Mobile phone",
            "type": "string"
        },
        "customInteger": {
            "permissions": {
                "SELF": "READ_WRITE"
            },
            "title": "customInteger",
            "type": "integer"
        }
    }
}
```



### User Profile object

#### User Profile properties

The User Profile object has several properties:

| Property   | Type                                                           | Description                                                                                                |
| ---------- | ---------------------------------------------------------------|----------------------------------------------------------------------------------------------------------- |
| `_embedded`  | Object                                   | If `expand`=`schema` is included in the request, the [User Profile Schema](#user-profile-schema-object) will be included in the response. |
| `_links`     | Object ([JSON HAL](http://tools.ietf.org/html/draft-kelly-json-hal-06)) | Discoverable resources related to the caller's user profile schema                                         |
| `createdAt`  | String (ISO-8601)                                                        | The timestamp the caller's account was created                                                             |
| `modifiedAt` | String (ISO-8601)                                                        | The timestamp the caller's account was last updated                                                        |
| `profile`    | Object                                       | The properties defined in the [User Profile Schema](#user-profile-schema-object)                                                                       |


#### User Profile example

```json
{
    "_links": {
        "describedBy": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/profile/schema"
        },
        "self": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount/directoryProfile"
        },
        "user": {
            "href": "https://${yourOktaDomain}/api/v1/myaccount"
        }
    },
    "createdAt": "2020-01-14T20:05:32.000Z",
    "modifiedAt": "2020-10-13T03:17:09.000Z",
    "profile": {
        "customBoolean": null,
        "foo": "bar",
        "login": "example@ex.ample.com",
        "mobilePhone": null,
        "customInteger": null
    }
}
```
