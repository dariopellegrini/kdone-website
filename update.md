---
layout: default
title: Update API
parent: API
nav_order: 3
---

# Update API

Once configured a module for a model, PATCH API will be available via http requests.

For instance, take this model as an example
```kotlin
data class Game(
    val name: String,
    val players: Int?): Identifiable()
```

Configure the module it's simply done by adding inside startKDone DSL
```kotlin
module<Game>("games")
```

To update a document, call the endpoint `games/:id`using a PATCH request, with new attributes' values in its body.

Endpoint
```
games/5e183d4333e63f6731d70b47
```

Method
```
PATCH
```

Body
```json
{
   "name":"CTR Nitro Fueled"
}
```

Result
```json
{
  "name": "CTR Nitro Fueled",
  "players": 7,
  "_id": "5e183d4333e63f6731d70b47"
}
```
