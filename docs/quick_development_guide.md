## Quick Develoment Guide
Although using the SDK makes easier and transparent integrating with a Digital Trust Protocol OP, if you want to directly interact without the SDK, here you will find some tips to help you implement your application integration.

- [Client Authentication][]
- [How to initiate a request][]
 - [Request][]
 - [Response][]
- [Redirection for authorization][]

## Client Authentication
For any communication between RP and OP we will use `private_key_jwt` as client credentials it will provide a strong security in this communication, details about how to generate it can be found [here](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication).

Here is an example of the `private_key_jwt`:
```
HEADER:
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "QuickJobs_KEY"
}
PAYLOAD:
{
  "sub": "gFvrD2m0CEnHHngFxqZwh",
  "iss": "gFvrD2m0CEnHHngFxqZwh",
  "aud": "https://op.iamid.io",
  "iat": 1583157415,
  "nbf": 1583157415,
  "exp": 1583158020,
  "jti": "QzPwMz_T1MgxnGEccmfok"
}
```

The encoded token will look like this:
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IlF1aWNrSm9ic19LRVkifQ.
eyJzdWIiOiJnRnZyRDJtMENFbkhIbmdGeHFad2giLCJpc3MiOiJnRnZyRDJtMENFbkhIbmdGeHFad2giLCJhdWQiOiJodHRwczovL29wLmlhbWlk
LmlvIiwiaWF0IjoxNTgzMTU3NDE1LCJuYmYiOjE1ODMxNTc0MTUsImV4cCI6MTU4MzE1ODAyMCwianRpIjoiUXpQd016X1QxTWd4bkdFY2NtZm9r
In0.
M0etMoZZNpx6MGeUEPQVgTaCmGopSKETTTTJwh7co_V-dFCkjC-_7MDAd-8nJrSPIFKKIe2-uNZYXjMOxmOjcsMvCtsCJLGb0MqKbpcMNElderlJm8uRQNqmnHdElaBlnl5MbHbr2EsNSqW_1dyo5cfrri3jJLs4T0ES6WUaZmryGOvF9qJdi32pWqeIdXEC30UMIBFJdKHAifKbnd47WSI8Bict
y0WrWrlMRemoiVwyRU3usrpAnCgpzHSQidqk6DmpaWiyq8QytCmWt3NRm7kUkQHQ4S1wyzauFIkgIWwgPgaOSg9nea46F6HdP_Je_O_QDLSa2ANzvAbMZ_A
```
Check instructions about [how to generate your development certificate and public keys](/docs/generate_keys).

## How to initiate a request

### Request
The request will have a request object, it can contain the same fields that are described in [OIDC Authentication Request](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest).

Here is an example of a request object to request standard claims:

```
{
  "aud": "https://op.iamid.io",
  "iss": "gFvrD2m0CEnHHngFxqZwh",
  "client_id": "gFvrD2m0CEnHHngFxqZwh",
  "response_type": "code",
  "redirect_uri": "http://localhost:8080",
  "nonce": "t9h1k8",
  "scope": "openid",
  "state": "myState",
  "claims": {
    "purpose": "In order to put you in contact with the employeer, we need to validate some details",
    "id_token": {
      "email": {
        "purpose": "We will use you email for contact purposes",
        "essential": false
      },
      "phone_number": {
        "purpose": "We will give your phone number to the employeer",
        "essential": true
      }
    }
  },
  "iat": 1583156662,
  "nbf": 1583156662,
  "exp": 1583157267,
  "jti": "YN3XGDBTDm66B5QQbW3jj"
}
```

This request object will be signed with the RP private key generated at registration time using the mechanism described [here](https://openid.net/specs/openid-connect-core-1_0.html#SignedRequestObject).

The encoded Request Object JWS will look like this:

```
eyJraWQiOiJRdWlja0pvYnNfS0VZIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ.
eyJhdWQiOiJodHRwczovL29wLmlhbWlkLmlvIiwiaXNzIjoiZ0Z2ckQybTBDRW5ISG5nRnhxWndoIiwiY2xpZW50X2lkIjoiZ0Z2ckQybTBDRW5I
SG5nRnhxWndoIiwicmVzcG9uc2VfdHlwZSI6ImNvZGUiLCJyZWRpcmVjdF91cmkiOiJodHRwOi8vbG9jYWxob3N0OjgwODAiLCJub25jZSI6IjBj
NmlkZCIsInNjb3BlIjoib3BlbmlkIiwic3RhdGUiOiJteVN0YXRlIiwiY2xhaW1zIjp7InB1cnBvc2UiOiJJbiBvcmRlciB0byBwdXQgeW91IGlu
IGNvbnRhY3Qgd2l0aCB0aGUgZW1wbG95ZWVyLCB3ZSBuZWVkIHRvIHZhbGlkYXRlIHNvbWUgZGV0YWlscyIsImlkX3Rva2VuIjp7ImVtYWlsIjp7
InB1cnBvc2UiOiJXZSB3aWxsIHVzZSB5b3UgZW1haWwgZm9yIGNvbnRhY3QgcHVycG9zZXMiLCJlc3NlbnRpYWwiOmZhbHNlfSwicGhvbmVfbnVt
YmVyIjp7InB1cnBvc2UiOiJXZSB3aWxsIGdpdmUgeW91ciBwaG9uZSBudW1iZXIgdG8gdGhlIGVtcGxveWVlciIsImVzc2VudGlhbCI6dHJ1ZX19
fSwiaWF0IjoxNTgzMTU3NDE1LCJuYmYiOjE1ODMxNTc0MTUsImV4cCI6MTU4MzE1ODAyMCwianRpIjoiVUxBSnkyZV9IR1UtRjhXVlhkUFR5In0.
cxyv7zmrsHwGuI7L1x3sgeCsz9HEWGsdg_RmYbPFBZhVV4a5CFotPVm91iWMO5lN-i1qjaWfjtsgSDjOukuKgndG7q-WZTdzVZbcHk5XLYT7smhE7Bnh8euqNVClWblA1POIJTX3MGbpTlFFIngfHvQav2rzNb1kLewZ4R3IhAAI8TzAyHoI5RGSeksVukDO7MJDaDeH8NmusSI4Is0CimzCntj5c0SOH8NA6LXRBJSHzH8OA4_oDKVtvAw3P8S-2najwj0AmSWTIEEnNRT2Gv6-P_q5-7_-
G6cKHe43I8z2DXD7i1McmKaHxO74GhcQB9cmg9s_OYbrWrgShzgeyJraWQiOiJRdWlja0pvYnNfS0VZIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyN
TYifQ.
eyJhdWQiOiJodHRwczovL29wLmlhbWlkLmlvIiwiaXNzIjoiZ0Z2ckQybTBDRW5ISG5nRnhxWndoIiwiY2xpZW50X2lkIjoiZ0Z2ckQybTBDRW5I
SG5nRnhxWndoIiwicmVzcG9uc2VfdHlwZSI6ImNvZGUiLCJyZWRpcmVjdF91cmkiOiJodHRwOi8vbG9jYWxob3N0OjgwODAiLCJub25jZSI6IjBj
NmlkZCIsInNjb3BlIjoib3BlbmlkIiwic3RhdGUiOiJteVN0YXRlIiwiY2xhaW1zIjp7InB1cnBvc2UiOiJJbiBvcmRlciB0byBwdXQgeW91IGlu
IGNvbnRhY3Qgd2l0aCB0aGUgZW1wbG95ZWVyLCB3ZSBuZWVkIHRvIHZhbGlkYXRlIHNvbWUgZGV0YWlscyIsImlkX3Rva2VuIjp7ImVtYWlsIjp7
InB1cnBvc2UiOiJXZSB3aWxsIHVzZSB5b3UgZW1haWwgZm9yIGNvbnRhY3QgcHVycG9zZXMiLCJlc3NlbnRpYWwiOmZhbHNlfSwicGhvbmVfbnVt
YmVyIjp7InB1cnBvc2UiOiJXZSB3aWxsIGdpdmUgeW91ciBwaG9uZSBudW1iZXIgdG8gdGhlIGVtcGxveWVlciIsImVzc2VudGlhbCI6dHJ1ZX19
fSwiaWF0IjoxNTgzMTU3NDE1LCJuYmYiOjE1ODMxNTc0MTUsImV4cCI6MTU4MzE1ODAyMCwianRpIjoiVUxBSnkyZV9IR1UtRjhXVlhkUFR5In0.
cxyv7zmrsHwGuI7L1x3sgeCsz9HEWGsdg_RmYbPFBZhVV4a5CFotPVm91iWMO5lN-i1qjaWfjtsgSDjOukuKgndG7q-WZTdzVZbcHk5XLYT7smhE7Bnh8euqNVClWblA1POIJTX3MGbpTlFFIngfHvQav2rzNb1kLewZ4R3IhAAI8TzAyHoI5RGSeksVukDO7MJDaDeH8NmusSI4Is0CimzCntj5c0SOH8NA6LXRBJSHzH8OA4_oDKVtvAw3P8S-2najwj0AmSWTIEEnNRT2Gv6-P_q5-7_-G6cKHe43I8z2DXD7i1McmKaHxO74GhcQB9cmg9s_OYbrWrgShzg
```

The following parameters will be sent:
- `client_assertion_type`: `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`
- `client_assertion`: will contain the credential generated as part of the client authentication
- `request_object`: will contain the Request Object JWS

Here is an example of the http request:

```
POST /initiate-authorize
Content-Type: application/x-www-form-urlencoded
User-Agent: PostmanRuntime/7.11.0
Accept: */*
Cache-Control: no-cache
Host: op.iamid.io
accept-encoding: gzip, deflate
content-length: 1886
Connection: keep-alive
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwtbearer&client_assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjI1OTMzN2RiLTc0MTItNDVkYS1hZDg2LWI2M2M5Nzc5
NjU4OCJ9.
eyJpc3MiOiJURVNULTI3NTRlZmE3NWU4YzRkMTFhNmQ3Zjk1YjkwY2Q4ZTQwLVRFU1QiLCJhdWQiOiJodHRwczovL29wLmlhbWlkLmlvIiwic3Vi
IjoiVEVTVC0yNzU0ZWZhNzVlOGM0ZDExYTZkN2Y5NWI5MGNkOGU0MC1URVNUIiwiaWF0IjoxNTcyNjExNjc0LCJuYmYiOjE1NzI2MTE2NzQsImV4
cCI6MTU3MjYxMTgwNCwianRpIjoiNzE5ZjFkNTctNmU2My00YjQ5LWEyNGEtNzAyZGM2MzZmMTgyIn0.
r53jI64EEnto391yRj2NAdwLRitsbhCZaTvBA8kHtUyvdDf3Fxog2LpVVZpAKedwEuWJGLEmga9mfyiRz_8kIrHX3vZvbNSTX7SJByKldaaWERfz
eWvRwt07w0JwpRGrEFH_GFjCB2tAtMUyUh8XOiF6bkS38hr1ludvOgqhC21ZMNp0uDGycdJY2Ge7fm4kXoZt8o9kI5yJiQDDZ_dSr3wvxFarKc5M
cHsrbpjbeb9vSHhGsP61u7Gt2upgnC1gqgli7ITul177BuKQc6tPQ8nh6O5oVGGvIMGbgvVmf5QE2Tg7_apNZIKIfuoLxEbavCiMQvBML0DYkQhO
skIYTg&request=eyJraWQiOiJRdWlja0pvYnNfS0VZIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ.
eyJhdWQiOiJodHRwczovL29wLmlhbWlkLmlvIiwiaXNzIjoiZ0Z2ckQybTBDRW5ISG5nRnhxWndoIiwiY2xpZW50X2lkIjoiZ0Z2ckQybTBDRW5I
SG5nRnhxWndoIiwicmVzcG9uc2VfdHlwZSI6ImNvZGUiLCJyZWRpcmVjdF91cmkiOiJodHRwOi8vbG9jYWxob3N0OjgwODAiLCJub25jZSI6IjBj
NmlkZCIsInNjb3BlIjoib3BlbmlkIiwic3RhdGUiOiJteVN0YXRlIiwiY2xhaW1zIjp7InB1cnBvc2UiOiJJbiBvcmRlciB0byBwdXQgeW91IGlu
IGNvbnRhY3Qgd2l0aCB0aGUgZW1wbG95ZWVyLCB3ZSBuZWVkIHRvIHZhbGlkYXRlIHNvbWUgZGV0YWlscyIsImlkX3Rva2VuIjp7ImVtYWlsIjp7
InB1cnBvc2UiOiJXZSB3aWxsIHVzZSB5b3UgZW1haWwgZm9yIGNvbnRhY3QgcHVycG9zZXMiLCJlc3NlbnRpYWwiOmZhbHNlfSwicGhvbmVfbnVt
YmVyIjp7InB1cnBvc2UiOiJXZSB3aWxsIGdpdmUgeW91ciBwaG9uZSBudW1iZXIgdG8gdGhlIGVtcGxveWVlciIsImVzc2VudGlhbCI6dHJ1ZX19
fSwiaWF0IjoxNTgzMTU3NDE1LCJuYmYiOjE1ODMxNTc0MTUsImV4cCI6MTU4MzE1ODAyMCwianRpIjoiVUxBSnkyZV9IR1UtRjhXVlhkUFR5In0.
cxyv7zmrsHwGuI7L1x3sgeCsz9HEWGsdg_RmYbPFBZhVV4a5CFotPVm91iWMO5lN-i1qjaWfjtsgSDjOukuKgndG7q-WZTdzVZbcHk5XLYT7smhE7Bnh8euqNVClWblA1POIJTX3MGbpTlFFIngfHvQav2rzNb1kLewZ4R3IhAAI8TzAyHoI5RGSeksVukDO7MJDaDeH8NmusSI4Is0CimzCntj5c0SOH8NA6LXRBJSHzH8OA4_oDKVtvAw3P8S-2najwj0AmSWTIEEnNRT2Gv6-P_q5-7_-G6cKHe43I8z2DXD7i1McmKaHxO74GhcQB9cmg9s_OYbrWrgShzg
```

### Response
The response of the call will be a expiration (in seconds) and a `request_uri` internal to the OP that will be used when calling the `/authorize` endpoint:

```
{
  "expires_in": 600,
  "request_uri": "urn:op.iamid.io:sdPTT1ixFvrJMIh13CjURy4Y0iBgnJ5AaYOV0Eo_q24"
}
```

## Redirection for authorization
With the previous response the RP will generate an URL to redirect the traffic using the OP authorize endpoint that can be found in the field
`authorization_endpoint` as part of the `openid-configuration` (sample sandbox url found in [https://live.iamid.io/.well-known/openidconfiguration](https://live.iamid.io/.well-known/openidconfiguration)), sending as parameters the `client_id`, `response_type`, `scope` and the `request_uri` returned by the OP in the `/initiate-authorize` endpoint. 

It is recommended to include the `state` parameter as part of the request to prevent SCRF attacks.

Here is an example of the redirection url:

```
https://op.iamid.iohttps://op-iamid-verifiedid-pro.e4ff.pro-eu-west-1.openshiftapps.com/web/login?request_uri=urn:op.iamid.io:
GnIw2VWxiMwVQ6WthUSUsczMCsHkcIMnfFYqYKESmD9
```

At this point flow will be managed by the OP to get consent from the customer

### Getting the id_token
Once the customer has consented the RP will get a call to the callback endpoint used in the request with the code to get the token, this callback must match the callback uri provided in RP registration.

An example can be found here:

```
http://127.0.0.1:8080/cb?code=ihgqHmeudvSANVkubv96JIVvj0RybzLbtT8DGJ4mw-t
```

If within the request you also specified an `state` attribute, you will get the value together with the code. You could use that state in your backend to identify the origin of the request (linking somehow with the user or process that originated the request on your end). Here is an example:

```
http://127.0.0.1:8080/cb?code=ihgqHmeudvSANVkubv96JIVvj0RybzLbtT8DGJ4mw-t&state=ABC0000121
```

This code can be used to get the id_token with the data from the `/token` endpoint.

To call the `/token` endpoint as in `initiate-authorize`, RP needs to use `private_key_jwt` as client credentials.

The following parameters will be sent:

- `grant_type`: `authorization_code`
- `redirect_uri`: RP redirect uri used in the request
- `client_assertion_type`: `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`
- `client_assertion`: will contain the credential
- `code`: code received from the RP

And here is an example request:

```
POST /token
Content-Type: application/x-www-form-urlencoded
User-Agent: PostmanRuntime/7.11.0
Accept: */*
Cache-Control: no-cache
Host: op-server-verifiedid-pro.e4ff.pro-eu-west-1.openshiftapps.com
accept-encoding: gzip, deflate
content-length: 974
Connection: keep-alive
grant_type=authorization_code&code=JSEpOlus0kCuvTqG8br61Bzq-RidonywGy3E8j2Li3T&redirect_uri=http%3A%2F%2F127.
0.0.1%3A8080%2Fcb&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwtbearer&client_assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjI1OTMzN2RiLTc0MTItNDVkYS1hZDg2LWI2M2M5Nzc5
NjU4OCJ9.
eyJpc3MiOiJURVNULTI3NTRlZmE3NWU4YzRkMTFhNmQ3Zjk1YjkwY2Q4ZTQwLVRFU1QiLCJhdWQiOiJodHRwczovL29wLmlhbWlkLmlvIiwic3Vi
IjoiVEVTVC0yNzU0ZWZhNzVlOGM0ZDExYTZkN2Y5NWI5MGNkOGU0MC1URVNUIiwiaWF0IjoxNTcyNjI0MTMwLCJuYmYiOjE1NzI2MjQxMzAsImV4
cCI6MTU3MjYyNDI2MCwianRpIjoiYjRjZWRjYTAtYWJhNy00NjZjLWI4YWQtODBjMTJjNGE5NDU1In0.
0wJnxXBAUtaS9KRy4jNDshhvv14pIdRyQmB7GH9Wkwjm7a706B2Y087zGcMqUrtdoOqNX2WbnFEySCXSDmH4fjIauTPVwa9qombAlxrT_x76vnLa
vqlgsr6Kn5l4_jbSuSMfSx_mWhiQIWKZL6PN5xVHb39pNo3cDKOSnzHgXvdK5KJALu8lqZog0eEujLF41gne9XBXDK5pDpZZAynsKMQN6G0by6cZ
uoy4KY-RhiUDhhIC-YiUs6ajSyjzySPrAaGomY4B3Z4kj2c6cMPh3QQK4IKnC6S535QJcoszH8Gv9damr3In2TEVrEjjnOXYkteKNAY13gipmACoamWNA
```

As result an `id_token` will be returned together with an access and a refresh tokens:

```
{
  "access_token": "SpBfSaSy5F7cbirHXbgCjYtwlc2fB_vctqzQN19qYAs",
  "expires_in": 1,
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Im9wX2tleV8xIn0. eyJzdWIiOiI3ODRiYWFiNDM5YTYwY2I0NzFjYWNiNDA1ODIxYWM4NGMwMGNiZjgwOTY3MGE0ZTExODliYzk1ZTQ3MTlmNDMzIiwidHhuIjoiVDdm SHdBZ1psVnNnNC1ONkFRdVZNIiwiZW1haWwiOiJsYXVyYUBzYW50YW5kZXJsYWJzLmlvIiwicGhvbmVfbnVtYmVyIjoiKzQ0MDAwMDAwMDAwMCIs Im5vbmNlIjoib2Y5NDJzIiwiYXRfaGFzaCI6Ii1heVh3VjV6SUk4SWZIRUlZUGdKU3ciLCJhdWQiOiJnRnZyRDJtMENFbkhIbmdGeHFad2giLCJl eHAiOjE1ODMxNTgzMzYsImlhdCI6MTU4MzE1NzczNiwiaXNzIjoiaHR0cHM6Ly9vcC5pYW1pZC5pbyJ9.h7iQi85TKJOZQ2T24tmIjtLOkR_F5- WtcpacMIVIAnZt91m3tC4i1bZKGKVzdtaH3i_6CTHt1UBW4sM_0TQSOHTI3_- 6U3Y4cBnCEFGb3DjKsZmRIqb73eSB72CM13hom1XNysjdj9bNMGU2nCg_JANakUTA8xiLyF5d8PWYXZKDRTQs0Tba0PZYn28wD4FDdSDcbjjudNE Exls6FWncAIajhCdT2o_vHvPhZ7_nGMzRmVmNPehiQxPW66aSAGkJhqOV0nk4RtKo_hw2MJdyygUiCCnDIlFewmCy296KUNe4Xpi8pYPWa7zTCGfFZFo6dUewMXm 70fp9NXN03abDQ",
  "scope": "openid",
  "token_type": "Bearer"
}
```

Inspecting the token this is what we get:

```
HEADER:
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "op_key_1"
}
PAYLOAD:
{
  "sub": "784baab439a60cb471cacb405821ac84c00cbf809670a4e1189bc95e4719f433",
  "txn": "T7fHwAgZlVsg4-N6AQuVM",
  "email": "laura@santanderlabs.io",
  "phone_number": "+440000000000",
  "nonce": "of942s",
  "at_hash": "-ayXwV5zII8IfHEIYPgJSw",
  "aud": "gFvrD2m0CEnHHngFxqZwh",
  "exp": 1583158336,
  "iat": 1583157736,
  "iss": "https://op.iamid.io"
}
```

The token signature must be validated using the OP public key that can be found using the `jwks_uri` url provided the in the [openid-configuration](https://opserver-verifiedid-pro.e4ff.pro-eu-west-1.openshiftapps.com/.well-known/openid-configuration)
