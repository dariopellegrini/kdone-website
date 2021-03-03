---
layout: default
title: Routes actions
nav_order: 5
---

# Routes actions

Inside modules' it's possible to add configurations that aim to execute code in relation to API calls.
It's like hooks of code that are executed before and after each API execution. They are useful to perform input check or actions after API calls, like sending an e-mail or a push notification after a creation.

```kotlin
module<Game>("games") {

   beforeCreate {
      headers, game ->
      println(headers)
   }

   afterCreate { headers, output ->
      println(output)
   }

   beforeGet { headers, query ->
      println(query)
   }

   afterGet { headers, query, games ->
      println(games)
   }

   beforeUpdate { headers, id, patch ->
      println(patch)
   }

   afterUpdate { headers, patch, game ->
      println(game)
   }

   beforeDelete { headers, id ->
      println(id)
   }

   afterDelete { headers, deleteResult ->
      println(deleteResult)
   }
}
```

For `userModule` 2 more actions are available, in order to support login and logout
```kotlin
userModule<User> {

   beforeLogin { headers, input ->
      println(input)
   }

   afterLogin { headers, input ->
      println(input)
   }

   beforeLogout { headers ->
      println(headers)
   }

   afterLogout { headers ->
      println(headers)
   }
}
```
