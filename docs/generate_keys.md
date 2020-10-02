To be able to secure the whole messages exchange between your application and the OP server, your application will need to cryptographically sign some of the request parameters which consists on JWT tokens. You will need to generate a certificate and use the private key to sign the tokens and give us your public key to validate them.

### Generating your private/public keys

For development, please generate a dummy private key not related to your production systems, you can easily generate a new one with the following
openssl command:

```bash
openssl genrsa -out private.pem 2048
```

Once you have your private key, you will be able to export the public key by running the following command:

```bash
openssl rsa -in private.pem -outform PEM -pubout -out publickey.pem
```

To register your application with the OP you will need to pass your public key. The format the public key will be accepted is as a JWK key, 
there are different ways for you to obtain the key as JWK, for demo purposes you could just simply go to [https://irrte.ch/jwt-js-decode/pem2jwk.html](https://irrte.ch/jwt-js-decode/pem2jwk.html) and paste your certificate (never paste production ones!!) and obtain something like this:

```json
{
  "kty": "RSA",
  "n": "qmeOGX8Xn01oTn9ij9KkOPEPztNcB_srxD13A1q6CMkmcov491aEW_jWUqjDcl4S1ioeb00xgCL3Rl1H_UYBlbmwh13ozC21aq_I4ZGALAIucCtnVTfuNLdo6JBhNllOWsJEFheuslhXLNbh_hoIpk7bU45177AGaTQgr9lJ2IMyxH4cgp
cTs_CtbXWovTBAba-YXEJ1kcCVnuoNESxsLRgkQaUyBZ-U-wwmQV5brEVmsy4Yy4ElAPIUyfc_ks2Lpy1N_-
rTrP34_HaMN7Z_W4GNV7KpxvzOc2jh0mKwWVAjzADyGNCuoxqFKPqunyQ8SvWNEg7Q9i07D9JDP9jVw",
  "e": "AQAB"
}
```

You could also use `pem-jwk` npm package tool from [here](https://www.npmjs.com/package/pem-jwk). Follow the instructions, basically run the following commands:

```bash
npm install -g pem-jwk
pem-jwk publickey.pem > public.jwk
```

If you need to generate jwk for your private key (you might need it depending on the code framework/language of your choice), just do the same passing this timethe private key pem file:

```bash
pem-jwk private.pem > private.jwk
```
