---
layout: default
title: Roles and Authorizations
parent: Users
nav_order: 2
---

# Roles and Authorizations

A model which inherits from `KDoneUser` has a `role` attribute that represents user's role.  
Using roles it's possible to manage different authorization levels on each model or user.

## Model authorization

Authorization are configure inside `module` DSL.  
There are different types of permissions: `create`, `read`, `update` and `delete`.  
Roles can be customise and are represented by the `role` attribute in user model. Other than those, there are 3 standard roles:
- `guest` represents the user not authenticated.
- `registered` represents the generic authenticated user, without a specific role.
- `owner` represent the owner of the resource.

In code authorizations are configured using a proper DSL

```kotlin
module<Game>("games") {

   authorizations {
      guest(read) // guest can read list of games

      registered(create, read) // registered can create a game and read list of games

      owner(read, update) // owner can read his games list and update his game

      "admin".can(create, read, update, delete) // admin can create, read, update and delete all games

      "officer".can(read, update) // officer can read and update all games
   }
}
```

If authorizations are configured, each API request will be checked thought Bearer Authentication token. If authorization check is passed, the API call is performed normally. Otherwise a `401 Unauthorized` error will be thrown.

## User authorization
User authorization works similarly to model authorization and, in addiction, needs for options that rules users' action upon other users. This is done using DSL as well.

```kotlin
userModule<User> {

   authorizations {

      // Guest can create a registered. In other words signup are permitted
      guest.can {
         registered(create)
      }

      // Registered can read other registered users
      registered.can {
         registered(read)
      }

      // Users can read, update themselves
      owner(read, update)

      // Admin can create, read, update and delete registered users
      // Admin can create, read, update and delete admin users
      "admin".can {
          registered(create, read, update, delete)
          "admin".with(create, read, update, delete)
      }
   }
}
```

If authorizations are configured, each user API request will be checked thought Bearer Authentication token. If authorization check is passed, the API call is performed normally. Otherwise a `401 Unauthorized` error will be thrown.
