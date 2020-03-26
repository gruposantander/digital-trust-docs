## Introduction

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

## How to initiate a request