---
layout: default
title: JWT Configuration
parent: Users
nav_order: 1
---

# JWT configuration

In KDone login works with JWT tokens, so it is necessary to configure JWT login before configure models and users.  
JWT configuration is done passing `JWTConfig` to `startKDone` function.

```kotlin
startKDone(
        port = 23146,
        mongoURL = "mongodb://localhost:27017/games",
        jwtConfig = JWTConfig("<secret>")) {

}
```

`JWTConfig` needs at least the secret string to sign JWT token. Other parameters are
- `issuer` string, by default set to "kdone.com"
- `validityInMs` represents JWT tokens validity, by default set to `null`, which means that JWT tokens never expire
- `algorithm` used to generate JWT tokens, by default set to HMAC512
