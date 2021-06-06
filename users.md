---
layout: default
title: Users
nav_order: 3
has_children: true
---

# Users

In KDone, users are a particular type of model. To create a user model, inherit from `KDoneUser`.

```kotlin
data class User(override var username: String,
                override var password: String?,
                val name: String,
                val surname: String,
                val nickname: String): KDoneUser()
```

> Attribute `password` is optional cause other types of authentication other than username and password are supported.

Inside `startKDone`function run
```kotlin
userModule<User> {
}
```

This opens API for authentication and CRUD on User model:

- Login API `users/auth/login`
- Create API `users`
- Get API `users`
- Patch API `users`
- Delete API `users`
- Logout API for a single session `auth/logout`
- Logout API for all sessions `auth/logout?all=true`
