### Request for native apps

When integrating from a mobile App, additional security needs to be added to prevent man in the middle attacks.

The request is almost the same, but it needs 2 extra fields defined in [RFC7636](https://tools.ietf.org/html/rfc7636):

- `code_challenge`: challenge code (string)
- `code_challenge_method`: defines the method used to generate the challenge 

```
{
  "response_type": "code",
  "redirect_uri": "com.appid://cb",
  "code_challenge": <codechallenge>,
  "code_challenge_method": "S256",
  "scope": "openid",
  "nonce": "abcdef",
  "claims": {
    "purpose": "This is why RP is asking for your information",
    "id_token": {
      "given_name": {
        "purpose": "This is why RP needs your name",
        "essential": true
      },
      "phone_number": {
        "purpose": "This is why RP needs your phone number",
        "essential": true
      },
      "birthdate": {
        "purpose": "This is why RP needs your DOB",
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
```

### How to generate the code Challenge and code Verifier for native apps

You can generate the code challenge and code verifier with this snippet:

```
import CommonCrypto

var buffer1 = [UInt8](repeating: 0, count: 32)
_ = SecRandomCopyBytes(kSecRandomDefault, buffer1.count, &buffer1)

let verifier = Data(bytes: buffer1).base64EncodedString()
    .replacingOccurrences(of: "+", with: "-")
    .replacingOccurrences(of: "/", with: "_")
    .replacingOccurrences(of: "=", with: "")
    .trimmingCharacters(in: .whitespaces)

guard let data = verifier.data(using: .utf8) else { return nil }

var buffer = [UInt8](repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
    data.withUnsafeBytes {
        _ = CC_SHA256($0, CC_LONG(data.count), &buffer)
    }
let hash = Data(bytes: buffer)
let challenge = hash.base64EncodedString()
    .replacingOccurrences(of: "+", with: "-")
    .replacingOccurrences(of: "/", with: "_")
    .replacingOccurrences(of: "=", with: "")
    .trimmingCharacters(in: .whitespaces)
```

Additionally this website can be use to generate PKCE for testing:

[https://tonyxu-io.github.io/pkce-generator/](https://tonyxu-io.github.io/pkce-generator/)

### How to obtain the token for native apps

When requesting the token, an extra parameter must be sent (`code_verifier`) this param will contain the verification code for the PKCE.

This is an example of the `POST` form parameters:

```
grant_type=authorization_code&code=qZ_9NqsZX-VLSw_YORX91Q1gQZ6rfaDWX7v9cA32R-f
redirect_uri=com.quickjobs%3A%2F%2Fidv%2Fsantander
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwtbearer&client_assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InpRNzlQT2Q3N3ZvZkRZdFBpcXBRSkZLLS1JOUJSRkds
VjJjbWkwSW12Sk0ifQ.
eyJpc3MiOiIyNDdERUE3QS0wNjAzLTQ4NUYtQTcxMi1DREU3RjBBRDkxMUEiLCJhdWQiOiJodHRwczovL29wLmlhbWlkLmlvIiwic3ViIjoiMjQ3
REVBN0EtMDYwMy00ODVGLUE3MTItQ0RFN0YwQUQ5MTFBIiwiaWF0IjoxNTczNzI5NDgyLCJuYmYiOjE1NzM3Mjk0ODIsImV4cCI6MTU3MzcyOTYx
MiwianRpIjoiODFmZTg4OTctM2MyMS00YWIxLWE3MDUtODkzZjE0NTA0NDI3In0.VySq1qTWeVoT05TQr4ltrBw9kAYMj2jocerwRezetnB9AVyExPPNstEA5Ln3JwlvtwFAUmedHeyX84qcAx8p0xjhVmBMj8KqgGNAfxFBaAiy1SMePwHq40tMtpelbN7j5qFKbICAZPFyJ6nW4AAfl8B_9
sNBO6hH5EtKaJMbI5KUbhHiBrjn2Gj7OOmWLu5VsqTuTISkN1SXKKhFdF7D2nU8TU6mTyKToTqXqDHoXZl3MuzCiK89fI2boCtB6mz1qxvQNTsGU9CH4tt82PXJ1lJddX4EiFw
SSBijKAKmGMCZfLCMXLfD9ojWmlkvrUmT8yB88jjtEnqWbDzh8Dog&code_verifier=blVUUrizdEZr6df9vfE70GEe6nD8KGX9gr77ItyI6bnpGgByRwnYW6ouyKQ7RR1z8L3piwKX2lBujZpsXW3Pf22G1PETpBK85G
GilgXcO6Amme4U3FBzDWGvIBFreAk1
```
