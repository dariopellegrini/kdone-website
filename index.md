---
layout: default
title: Home
nav_order: 1
description: "Welcome in KDone"
permalink: /
---


# Welcome to KDone website


KDone is a configurator to implement RESTful API very easily. It is on top of Ktor framework and uses its core principles like functional programming and DSL.

KDone supports at the moment only MongoDB and JWT token authentication.

## Getting started
### Installation
KDone can be installed using Gradle.

First add nitpick to Gradle repositories

```groovy
repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
```

Then import KDone dependency
```groovy
implementation 'com.github.dariopellegrini:KDone:v0.5.1'
```

### Configuration
 The simplest KDone configuration needs a model to represent database data. It is suggested that this model inherits from Identifiable class, in order to have automatically managed MongoDB _id property.

```kotlin
data class Game(
    val name: String,
    val players: Int?): Identifiable()
```

Then using DSL the configuration of CRUD API is pretty straight forward.  
In main, call startKDone function, passing as arguments the desired port, mongo URL and a configuration for JWT with the secret used for token signature.  
Finally with DSL approach declare a module with the desired model class and its endpoint.

```kotlin
startKDone(
        port = 23146,
        mongoURL = "mongodb://localhost:27017/games",
        jwtConfig = JWTConfig(JWT.secret)) {

   module<Game>("games")

}
```

This configuration will give CRUD API at "games" endpoint

- POST http://localhost:23146/games performs creation of a new game
- GET http://localhost:23146/games returns the list of all games
- GET http://localhost:23146/games/:id returns the game with the specified id
- PATCH http://localhost:23146/games/:id performs an update on the game with the specified id
- DELETE http://localhost:23146/games/:id deletes the game with the specified id

### Alternative starting methods
Instead that specify MongoDB URL in `startKDone`function, another way to start KDone is to use
MongoDatabase instance from KMongo library. KDone uses KMongo as MongoDB driver.
```kotlin
val mongoDatabase = KMongo.createClient().getDatabase("database")
startKDone(
   port = System.getenv("PORT")?.toInt() ?: 23146,
   mongoDatabase = mongoDatabase,
   jwtConfig = JWTConfig(JWT.secret)) {
}
```
Since KDone uses Ktor, it is possible to start KDone inside a Ktor configuration through `installKDone` function.
```kotlin
embeddedServer(Netty, port) {
   installKDone(mongoURL, jwtConfig) {
   }
}.start(wait = true)
```
