---
layout: default
title: Hash strategy
parent: Users
nav_order: 4
---

# Hash strategy

User's passwords are hashed by default using standard SHA512. This algorithm can be modified using `hashStrategy` configuration inside `userModule` DSL. It consists of 2 functions, one which creates the hash and one which checks if a password and a hash are verified together. This works for KDone's password authentication.

```kotlin
userModule<User> {

   hashStrategy(
      hash = {
         BCryptUtils.hash(it, 10)
      },
      verify = { password, hash ->
         BCryptUtils.verify(password, hash)
      }
   )
}
```
