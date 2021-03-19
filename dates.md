---
layout: default
title: Dates
nav_order: 5
---

# Dates

Every model can implement `DateModel` interface, which has 2 attributes:

- `dateCreated` for creation date
- `dateUpdated` for update date

```kotlin
data class Game(
    val name: String,
    val players: Int?,
    val image: ResourceFile?): Identifiable(), DateModel {
    override var dateCreated = Date()
    override var dateUpdated = Date()
}
```

After every creation, `dateCreated` will be assigned with the current date.
After every update, `dateUpdated` will be updated with the current date.
