---
layout: default
title: Localization
nav_order:9 
---

# Localization

Localized content can be added to every model using `Localized<T>` class, which takes as generic the class that should be localized.
Giving a model attribute `Localized<T>` type turns that attribute in a map with languages' codes as keys and values the corresponded translated values.

Let's see an example.

The model
```kotlin
class Book(val name: String)
```

can be localized turning `String` into `Localized<String>`
```kotlin
class Book(val name: Localized<String>)
```

After that the creation API will take a body with `name` as a localized object.
```json
{
   "name": {
     "en": "The Lord of the Rings",
     "it": "Il Signore degli Anelli"
     }
}
```

Read API will then return the localized object
```json
{
  "name": {
    "en":"The Lord of the Rings",
    "it":"Il Signore degli Anelli"
  },
  "_id": "6048af288110c462c3580ec0",
  "owner": null
}
```

Then if read API request has `Accept-Language` header with one of the available language codes, the API will respond with a normal string with the selected language.

- `Accept-Language: en`
```json
{
    "name": "The Lord of the Rings",
    "_id": "6048af288110c462c3580ec0",
    "owner": null
  }
```

- `Accept-Language: it`
```json
  {
    "name": "Il Signore degli Anelli",
    "_id": "6048af288110c462c3580ec0",
    "owner": null
  }
```

If `Accept-Language` value is not present in the localized object, the response will contain the English (`en`) value.  
If the English value is not present, the response will contain the first available value.
