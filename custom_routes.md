---
layout: default
title: Custom routes
nav_order: 10
---

# Custom routes

KDone let to add custom routes, giving developers some helper function to interact easily with KDone structure, such as authentication or database.

## Adding custom routes
The most simple thing that can be done is add a route with a custom code. KDone is on top of <a href="https://ktor.io" target="_blank">Ktor</a>, so routes are implemented using Ktor DSL. They can be added inside `module` closure or outside it, inside `startKDone` closure.

```kotlin
startKDone(
        port = 23146
        mongoURL = "mongodb://localhost:27017/games",
        databaseName = "games",
        jwtConfig = JWTConfig(JWT.secret)) {
  
          get("ciao") {
            call.respond(HttpStatusCode.OK, mapOf("response" to "Ciao!")
          }
                           
          module<Game>("games") {
            ...
  
            get("hello") {
              call.respond(HttpStatus.OK, mapOf("response" to "Hello!")
            }
          }
        }
```

## Using authentication
Custom routes can be authenticated using `authenticateJWT` function. Inside that the authenticated data of a user can be retrieved with `userAuth` function.

```kotlin
authenticateJWT {
  get("user/games") {
    val userAuth = call.userAuth.userId
    call.respond(HttpStatusCode.OK, mapOf("response" to userAuth)
  }
```

Authentication could be optional.

```kotlin
authenticateJWT(optional = true) {
  get("user/games") {
    val userAuth = call.userAuthOrNull.userId
    call.respond(HttpStatusCode.OK, mapOf("response" to userAuth)
  }
}
```

## Using repositories
In order to access the database, KDone offers a couple of ways to use repositories connected to MongoDB.  
Each model has a repository that lets to access MongoDB query on its collection, through <a href="https://litote.org/kmongo" target="_blank">KMongo</a> API.
To access the repository in `module` closure, just use `repository` instance available in configuration closure.

```kotlin
module<Game>("games") {
  get("user/games") {
    val result = repository.findAll() // Games list
    call.respond(HttpStatusCode.OK, mapOf("games" to result))
  }
}
```

To access the repository outside `module` closure, KDone has a `Route` extension that can be used inside `startKDone` function or inside every Ktor `routing`.

```kotlin
startKDone(
        port = 23146
        mongoURL = "mongodb://localhost:27017/games",
        databaseName = "games",
        jwtConfig = JWTConfig(JWT.secret)) {
  val gamesRepository = mongoRepository<Game>("games")
  
  get("user/games") {
    val result = gamesRepository.findAll() // Games list
    call.respond(HttpStatusCode.OK, mapOf("games" to result))
  }
}
```

```kotlin
routing {
  val gamesRepository = mongoRepository<Game>("games")
  
  get("user/games") {
    val result = gamesRepository.findAll() // Games list
    call.respond(HttpStatusCode.OK, mapOf("games" to result))
  }
}
```

## Converting data
In KDone, models represent the documents saved on MongoDB. It can be useful to convert them easily into another model to deal with permission, different views and so on.  
KDone has the `transfer` function, that can turn a model into another.  
Let's take a `User` model.

```kotlin
data class User(override var username: String,
                val name: String,
                val surname: String,
                val nickname: String,
                val image: ResourceFile?,
                val active: Boolean?,
                val score: Double?,
                val gameId: Id<Game>?,
                val date: Date?,
                val location: GeoLocation?) : KDoneUser(
```

Then a public version of it.

```kotlin
data class PublicUser(val username: String,
                val name: String,
                val surname: String,
                val nickname: String,
                val image: ResourceFile?)
```

Using `transfer` function is possible to convert the first into the second.

```kotlin
val user = usersRepository.findAll().first()

// New model created manually
val publicUser = user.transfer<User, PublicUser> {
        _, currentUser ->
                PublicUser(currentUser.username,
                        currentUser.name,
                        currentUser.surname,
                        currentUser.nickname,
                        currentUser.image)
      }

// New model created automatically, resolving attributes with the same names
val publicUser: PublicUser = user.transfer()
```
