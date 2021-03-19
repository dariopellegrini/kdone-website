---
layout: default
title: Delete API
parent: API
nav_order: 4
---

# Delete API

Once configured a module for a model, DELETE API will be available via http requests.

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

To delete a document, call the endpoint `games/:id`using a DELETE request.

Endpoint
```
games/5e183d4333e63f6731d70b47
```

Method
```
DELETE
```

Result
```json
{
  "deletedCount": 1
}
```
