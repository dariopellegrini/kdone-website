---
layout: default
title: Read API
parent: API
nav_order: 2
---

# Read API

Once configured a module for a model, GET API will be available via http requests.

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

To read the list of games just call via http the endpoint `games`.
The result will be like this
```json
[
  {
    "name": "Red Dead Redemption 2",
    "players": 1,
    "_id": "5e18372d9781db4d055afaf9"
  },
  {
    "name": "Horizon: Zero Dawn",
    "players": 1,
    "_id": "5e1837939781db4d055afafb"
  },
  {
    "name": "CTR",
    "players": 7,
    "_id": "5e183d4333e63f6731d70b47"
  }
]
```

To retrieve a single value call `games/:id`, i.e. `games/5e18372d9781db4d055afaf9`.
```json
{
  "name": "Red Dead Redemption 2",
  "players": 1,
  "_id": "5e18372d9781db4d055afaf9"
}
```

## Queries
Query are available using get parameters on model attributes.
`games?players=1`.
```json
[
  {
    "name": "Red Dead Redemption 2",
    "players": 1,
    "_id": "5e18372d9781db4d055afaf9"
  },
  {
    "name": "Horizon: Zero Dawn",
    "players": 1,
    "_id": "5e1837939781db4d055afafb"
  }
]
```

## Advanced query
MongoDB queries are supported using `_query` GET parameter.  
For example `games?_query={$or:[{players:1}, {players:7}]}`.
```json
[
  {
    "name": "Red Dead Redemption 2",
    "players": 1,
    "_id": "5e18372d9781db4d055afaf9"
  },
  {
    "name": "Horizon: Zero Dawn",
    "players": 1,
    "_id": "5e1837939781db4d055afafb"
  },
  {
    "name": "CTR",
    "players": 7,
    "_id": "5e183d4333e63f6731d70b47"
  }
]
```
