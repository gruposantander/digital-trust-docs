To register the application, simply customise the following request to accommodate your application details, passing also the JWK from [previous step](#annex-5-generate-your-publicprivate-keys), and including as `kid` whatever is matching at your side (just in case you are using a key store or configuration library expecting that Id as key):

### Dynamic Client Registration Details

```json
{
    "client_name": "Demo Sample App",
    "logo_uri": "https://www.santanderlabs.io/storage/uploads/2019/07/16/5d2dd97f630afmoney-Monsters.svg",
    "policy_uri": "https://www.santanderlabs.io/en/privacy-policy",
    "tos_uri": "https://www.santanderlabs.io/en/terms-and-conditions",
    "redirect_uris": [
        "http://localhost:8080"
    ],
    "application_type": "web",
    "token_endpoint_auth_method": "private_key_jwt",
    "token_endpoint_auth_signing_alg": "RS256",
    "jwks": {
        "keys": [
            {
                "kty": "RSA",
                "e": "AQAB",
                "kid": "DEMOAPP_KEYID",
                "n": "qmeOGX8Xn01oTn9ij9KkOPEPztNcB_srxD13A1q6CMkmcov491aEW_jWUqjDcl4S1ioeb00xgCL3Rl1H_UYBlbmwh13ozC21aq_I4ZGALAIucCtnVTfuNLdo6JBhNllOWsJEFheuslhXLNbh_hoIpk7bU45177AGaTQgr9lJ2IMyxH4cgp
cTs_CtbXWovTBAba-YXEJ1kcCVnuoNESxsLRgkQaUyBZ-U-wwmQV5brEVmsy4Yy4ElAPIUyfc_ks2Lpy1N_-
rTrP34_HaMN7Z_W4GNV7KpxvzOc2jh0mKwWVAjzADyGNCuoxqFKPqunyQ8SvWNEg7Q9i07D9JDP9jVw"
            }
        ]
    }
}
```
### Request
And here is the request passing that data:

```
curl -X POST /reg \
 -H 'Accept: */*' \
 -H 'Content-Type: application/json' \
 -d '<<CLIENT_REGISTRATION_DETAILS>>'
```

### Response
As a result for that request you will receive together with the parameters above provided, your `client_id` plus a `registration_access_token` to be able to obtain and modify your application details.

```
{
    ...
 "client_id_issued_at": 1578654878,
 "client_id": "IhRhCPwnPikoCOJdAhyEp",
 "registration_client_uri": "https://live.iamid.io/reg
/IhRhCPwnPikoCOJdAhyEp",
 "registration_access_token": "w8__DQ0lMSfkIAa9z_Mzwb3EP8gPD5jIzmoHIX1cA70"
}
```

Bear in mind that your application details will be use not only for validating security (see getting started guide for details) but also to display your demo app name, logo, terms and conditions and policy links, and very important, your `redirect_uri`. You app will be redirected to that endpoint passing the `access_code` once the customer logon and consent application is done. 