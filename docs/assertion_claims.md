### Request
To request data verification without disclosing the claim values, an `assertion_claims` element must be used. 

Here is a sample request for obtaining assertion claims in the `id_token`, together with standard claims:

```
{
  "response_type": "code",
  "redirect_uri": "http://127.0.0.1:8080/cb",
  "scope": "openid",
  "nonce": "abcdef",
  "claims": {
    "purpose": "This is why RP is asking for your information",
    "id_token": {
      "assertion_claims": {
        "given_name": {
          "assertion": {
            "eq": "John Wall"
          },
          "purpose": "This is why RP is verifying your name"
        },
        "address": {
            "props": {
                "country": { "eq": "UK" }
            },
            "purpose": "This is why RP is verifying your country address"
        },
        "age": {
          "assertion": {
            "gte": 65, "lte": 14
          },
          "purpose": "This is why RP is verifying your age range"
        }
      },
      "phone_number": {
        "purpose": "This is why RP needs your phone number",
        "essential": true
      }
    }
  }
}
```

Check [specifications](/assertions/claim-assertions-00.html) for full details on available operations and ways to build assertions.

### Response

Here is an example of an `id_token` with `assertion_claims` in the response:

```
HEADER:
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "op_key_1"
}
PAYLOAD:
{
  "sub": "ec20142d7fb104b9b5b7c0377684189231072178f3029d9efef21bb07c341203",
  "assertion_claims": [
    "give_name": { "result": false},
    "address": { "result": true},
    "age": { "result": true}
  ],
  "phone_number": "+447698456251",
  "at_hash": "PRri2Kv1M2doVBMc49yIvQ",
  "aud": "TEST-2754efa75e8c4d11a6d7f95b90cd8e40-TEST",
  "exp": 1572867162,
  "iat": 1572863562,
  "iss": "https://op.iamid.io"
}
```