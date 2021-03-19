---
layout: default
title: Read API
parent: API
nav_order: 2
---

# Read API

Once configured a module for a model, GET API will be available via http requests.

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
Queries are available using get parameters on model attributes.
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

## Lookup
In MongoDB join between collection are not possible. In their place, there is lookup operator, that let to add an element of other collection referencing them through field names.  
The most common use case is to add in a document of a collection a reference to one or more id of another collection and then perform lookup, to integrate the referenced document of the second collection into the queries' results of the first one.

Lookup is supported using `Lookup` annotation.  

These models `Game` referrs `Platform` through its `Id`.
```kotlin
data class Game(val name: String, val platformId: Id<Platform>)
    
data class Platform(val name: String, val brand: String): Identifiable()
```

The JSON result of the document
```json
{
  "name": "Days Gone",
  "players": 1,
  "platformId": "604fbff21e10265e6b8dcd14",
  "_id": "604fc0121e10265e6b8dcd16",
  "dateCreated": "2021-03-15T21:14:10.010Z",
  "dateUpdated": "2021-03-15T21:14:10.010Z"
}
```

Lookup can be configured using `Lookup` annotation, with referral parameter and collection name.
```kotlin
data class Game(val name: String, val platformId: Id<Platform>) {
     @Lookup("platformId", "platforms")
    val platform: Platform? = null
}
```

Then any GET request with `_lookup` parameters set to `true` will have the referenced `platform` document inside JSON result for list or detail.
```json
{
  "name": "Days Gone",
  "players": 1,
  "platformId": "604fbff21e10265e6b8dcd14",
  "_id": "604fc0121e10265e6b8dcd16",
  "dateCreated": "2021-03-15T21:14:10.010Z",
  "dateUpdated": "2021-03-15T21:14:10.010Z",
  "platform": {
    "name": "Playstation 4",
    "brand": "Sony",
    "_id": "604fbff21e10265e6b8dcd14",
    "owner": null
  }
}
```

The parameter `_lookup` can be omitted using `autolookup` in route configuration.
```kotlin
module<Game>("games") {
     
     ...
     
     autolookup = true
     
     ...
     
}
```
