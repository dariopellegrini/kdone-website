---
layout: default
title: Data Transfer Object (DTO)
nav_order: 8
---

# Data Transfer Object (DTO)

KDone supports DTO pattern, which allows controlling API's input and output data. It is possible to specify the output model of reading operations and the input model of creating and update operations.
To configure DTO, add `dto` inside `module` configuration DSL

```kotlin
module<Game>("games") {
...

   dto {
      ...
   }
}
```

### Read
Let's say we have API configured for this model:
```kotlin
data class Game(
    val name: String,
    val players: Int?,
    val image: ResourceFile?): Identifiable()
```

If we call a GET API, the result will be
```json
  {
    "name": "Days Gone",
    "players": 1,
    "image": {
      "url": "https://dariopellegrini.ams3.digitaloceanspaces.com/kdone/game/1578676851711aozI0SMf9C-Days-Gone-768x432.jpg",
      "mimeType": "image/jpeg"
    },
    "_id": "5e18b27480c3a94962fa09a9"
  }
```

Now we would like to specify a custom model for GET APIs, different from the previous one, such as the output has only `name` and `players` parameters.

This can be achieved by configuring KDone DTO.

First create the desired model

```kotlin
data class GameOutput(val name: String, val players: Int)
```

Then configure inside `dto`

```kotlin
dto {
   read<GameOutput> {
      game ->
      GameOutput(game.name, game.players)
   }
```

Inside `read` DSL is specified the rule used to go from `Game` to `GameOutput`. It is possible to omit the rule and KDone will make the transfer automatically, using the class attributes' name.
```kotlin
dto {
   read<GameOutput>()
}
```

Now each KDone GET API for `Game` model will have the desired output.
```json
{
    "name": "Days Gone",
    "players": 1
}

```

### Create
Create DTO works opposite from read one: it is possible to specify the input model of create APIs, in order to control the JSON parameters of POST calls.

Again let's take as an example `Game` class define previously.
We would like to let POST specify only the parameter `name`.  

To do this start creating a model
```kotlin
data class GameInput(val name: String)
```

Then configure create DTO
```kotlin
create<GameInput> {
   gameInput ->
   Game(gameInput.name, 1, null)
}
```

The rule is used to go from `GameInput` to `Game`.

Like for read DTO, it is possible to omit the rule and let KDone perform an automatic transfer from `GameInput` to `Game` using class attributes' names.

Now the new POST supported body will be
```json
{
   "name":"Days Gone"
}
```

### Update
Similarly to create one, update DTO works on input, but doesn't need any rule. It only needs the input model that PATCH calls' body should have.

Create the model
```kotlin
data class GameUpdate(val name: String)
```

Configure update DTO
```
dto {
   update<GameUpdate>()
}
```

Now each PATCH API should have a body like
```
{
   "name":"Days Gone"
}
```

## Role-based DTO
It is possible to configure DTO for each user role. This gives a more specific control on user's input and output.
```kotlin

// Guest DTO used when the call has no authorization Bearer token
guest {
   read<GameOutput> {
      game ->
     GameOutput(game.name)
   }

   create<GameInput>() {
      gameInput ->
      Game(gameInput.name, null, null)
   }

   update<GameUpdate>()
}

// Registered DTO used when the call has authorization Bearer token
registered {
   read<GameOutput> {
      game ->
      GameOutput(game.name, game.players)
   }

   create<GameInput> {
      gameInput ->
      Game(gameInput.name, gameInput.players, null)
   }

   update<GameUpdate>()
}

// Owner DTO used when user owns a specific resource
// Owner doesn't need any create DTO, cause owner is assigned after creation
owner {
   read<GameOutput> {
      game ->
      GameOutput(game.name, game.players, game.image)
   }

   update<GameUpdateOwner>()
}

// Custom role DTO used when a user has this role
"admin".dto {
   read<GameOutput> {
      game ->
      GameOutput(game.name, game.players, game.image)
   }

   create<GameInput> {
      gameInput ->
      Game(gameInput.name, gameInput.players, gameInput.image)
   }

   update<GameUpdate>()
}
```

### Priority
If DTO authorizations overlap, KDone uses priority algorithms.

#### Read
```kotlin
if (userRole != null)
   if (dtoIsPresentForRole(userRole)) useDTOForRole(userRole)
   else if (user.isOwnerOf(resource)) useDTOForOwner()
   else useGlobalDTOIfPresent()
else if (userToken != null)
   if (user.isOwnerOf(resource)) useDTOForOwner()
   else if (dtoForRegisteredUserIsPresent()) useRegisteredUserDTO()
   else useGlobalDTOIfPresent()
else if (dtoForGuestUserIsPresent) useGuestUserDTO()
else useGlobalDTO()
```

#### Create
```kotlin
if (userRole != null)
   if (dtoIsPresentForRole(userRole)) useDTOForRole(userRole)
   else useGlobalDTOIfPresent()
else if (userToken != null)
   if (user.isOwnerOf(resource)) useDTOForOwner()
   else useGlobalDTOIfPresent()
else if (dtoForGuestUserIsPresent) useGuestUserDTO()
else useGlobalDTO()
```

#### Update
```kotlin
if (userRole != null)
   if (dtoIsPresentForRole(userRole)) useDTOForRole(userRole)
   else if (user.isOwnerOf(resource)) useDTOForOwner()
   else useGlobalDTOIfPresent()
else if (userToken != null)
   if (user.isOwnerOf(resource)) useDTOForOwner()
   else if (dtoForRegisteredUserIsPresent()) useRegisteredUserDTO()
   else useGlobalDTOIfPresent()
else if (dtoForGuestUserIsPresent) useGuestUserDTO()
else useGlobalDTO()
```
