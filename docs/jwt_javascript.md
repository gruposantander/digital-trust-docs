For JWT manipulation (generate, parse and validation) in Javascript there exist a very good library [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) created by auth0.

**Tip**:
A great resource to find jwt libraries in all languages with the featured that this libraries implement you can have a look to the web [https://jwt.io](https://jwt.io), section "Libraries".

### Generating a private_key_jwt 

```javascript
const jwt = require('jsonwebtoken')
const fs = require('fs')
const privKey = fs.readFileSync('./test/resources/privateKey.pem') // Private key in pem format
const payload = {
    aud: 'https://op.iamid.io',
    iss: 'TEST-2754efa75e8c4d11a6d7f95b90cd8e40-TEST',
    sub: 'TEST-2754efa75e8c4d11a6d7f95b90cd8e40-TEST'
}
let jwtCounter = 0
jwt.sign(payload, privKey, {
    algorithm: 'RS256',
    keyid: '259337db-7412-45da-ad86-b63c97796588',
    expiresIn: 30,
    notBefore: 0,
    jwtid: `jwt-${jwtCounter++}` // This should be a random
    // or counter to avoid generate two jwt with the same number
})
```

### Generate a request object

```javascript
const jwt = require('jsonwebtoken')
const fs = require('fs')
const privKey = fs.readFileSync('./test/resources/privateKey.pem') // Private key in pem format
const payload = {
    "response_type": "code",
    "redirect_uri": "http://127.0.0.1:8080/cb",
    "scope": "openid",
    "claims": {
        "id_token": {
            "given_name": {
                "essential": true
            },
            "email": {
                "essential": true
            }
        },
        "userinfo": {
            "birthdate": {
                "essential": true
            },
        }
    }
}
let jwtCounter = 0
jwt.sign(payload, privKey, {
    algorithm: 'RS256',
    keyid: '259337db-7412-45da-ad86-b63c97796588',
    expiresIn: 30,
    notBefore: 0,
    jwtid: `jwt-${jwtCounter++}` // This should be a random
    // or counter to avoid generate two jwt with the same number
})
```

### Validating the id_token
```javascript
const jwt = require('jsonwebtoken')
const fs = require('fs')
const publicKey = fs.readFileSync('./test/resources/publicKey.pem') // Public key in pem format
const idToken = "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Im9wX2tleV8xIn0.
eyJzdWIiOiJlYzIwMTQyZDdmYjEwNGI5YjViN2MwMzc3Njg0MTg5MjMxMDcyMTc4ZjMwMjlkOWVmZWYyMWJiMDdjMzQxMjAzIiwiZ2l2ZW5fbmFt
ZSI6IkV2ZWxpbmUiLCJ0b3RhbF9iYWxhbmNlIjp7ImFtb3VudCI6MjkyMy43NCwiY3VycmVuY3kiOiJHQlAifSwiZW1haWwiOiJkYXZpZGUuYmlh
bmNoaW5pQHNhbnRhbmRlci5jby51ayIsInBob25lX251bWJlciI6IjAwMDAwMDAwMDAiLCJhdF9oYXNoIjoiSXZNNGo1ak9RajVDNzlacXRlc2h2
QSIsImF1ZCI6IlRFU1QtMjc1NGVmYTc1ZThjNGQxMWE2ZDdmOTViOTBjZDhlNDAtVEVTVCIsImV4cCI6MTU3MzEyMzExNywiaWF0IjoxNTczMTE5
NTE3LCJpc3MiOiJodHRwczovL29wLmlhbWlkLmlvIn0.
    ebX4ePW8k8dgYjvZGqmXRwmVa9dYP1StQbMMzW4bBLv8vTEJQrI4YY7RR5ZNjJU8mb1ktX6bhQYnpXuWmeO3PumhyRZkjlbVn9iDQur65WZALkFJ
9aRXMGYxXB9uJXLx7wv_b4BY7uZhKont8b6cwhAKl7BCvMBDaG0MILcnaZFFJA31GlchykQTKjo1poFadoERwhmRiAe2n0 -
    0HPIeqjxY8IhBEMb08Z3EK - ONMqF11VuU8S3ZmXOOfgudfTHLbfm4MbH2 - zHMP_2 - iheXBYWGTSPKNuyz0nU8aOz5nHxIp9t16pr - AuAboipVqe1xnAnQwPMqGCYZxZQIQ6Kxw"
// WIll throw and error if not valid jwt
const decodedIdToken = jwt.verify(idToken, publicKey)
```