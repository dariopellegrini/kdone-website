---
layout: default
title: Creation API
parent: API
nav_order: 1
---

# Creation API

Once configured a module for a model, POST API will be available via http requests.

For instance take this model as example
```kotlin
data class Game(
    val name: String,
    val players: Int?): Identifiable()
```

Configure the module it's simply done by adding inside startKDone DSL
```kotlin
module<Game>("games")
```

To add a Game document in MongoDB use an http POST request.  

Endpoint
```
games
```

Method
```
POST
```

Body
```json
{
    "name": "CTR",
    "players": 7,
    "_id": "5e183d4333e63f6731d70b47"
}
```

Result
```json
{
  "name": "Horizon: Zero Dawn",
  "players": 1,
  "_id": "5e1837939781db4d055afafb"
}
```

The model has as attributes
- name a non optional `String`
- players an optional `Ìnt`

This means that in the body passed to the POST request `name`is mandatory and `players`is optional.  

This is permitted
```json
{
   "name":"CTR"
}
```

This will return a `400 Bad request` error
```json
{
   "players":7
}
```
