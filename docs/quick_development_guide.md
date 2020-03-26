## Introduction
Although using the SDK makes easier and transparent integrating with a Digital Trust Protocol OP, if you want to directly interact without the SDK, here you will find some tips to help you implement your application integration.

## Client Authentication
For any communication between RP and OP we will use private_key_jwt as client credentials it will provide a strong security in this communication, details
about how to generate it can be found [here](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication).

Here is an example of the private_key_jwt:
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
Check instructions below in annex section about how to generate your development certificate and public keys.

## How to initiate a request

### Request
The request will have a request object, it can contain the same fields that are described in [OIDC Authentication Request](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest).

Here is an example of a request object to request Claims:

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
- client_assertion_type: urn:ietf:params:oauth:client-assertion-type:jwt-bearer
- client_assertion: will contain the credential generated as part of the client authentication
- request_object: will contain the Request Object JWS

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
The response of the call will be a expiration (in seconds) and a request_uri internal to the OP that will be used when calling the /authorize endpoint:

```
{
  "expires_in": 600,
  "request_uri": "urn:op.iamid.io:sdPTT1ixFvrJMIh13CjURy4Y0iBgnJ5AaYOV0Eo_q24"
}
```