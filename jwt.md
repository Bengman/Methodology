# JSON Web Token (JWT)

## Header

- [ ] Check if we can set the "alg" to none and remove the signature.
- [ ] Check for any injection in the "kid" parameter.
- [ ] Check if embedded "jwk" elements are trusted.
- [ ] Check for whitelisting of algorithms.
- [ ] Check for any replay protecttion by the "jti" element.


## Body
- [ ] Check for any sensetive information stored in the body.
- [ ] Verify token expiry in the "iat" and "exp" elements


## Signature

- [ ] Try removing or tampering with the signature to verify that it is enforced.
- [ ] Try to brute-force the secret key.
- [ ] Check for time constant verification for HMAC.
- [ ] Check for any keys or secrets in the source code.
- [ ] Check that keys and secrets are different between environments.
