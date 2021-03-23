---
layout: default
title: Geolocation
nav_order: 6
---

# Geolocation

Adding `Geolocation` attribute to a model enables Geo queries through `_query` parameter inside a `GET` request.

```kotlin
data class Poi(val name: String,
               val description: String,
               val location: GeoLocation)
```

This will add a `Geolocation` attribute to API requests.  
The body will be made like that
```json
{
   "name":"McDonald's",
   "description":"McDonald's in this place is very useful and good for drivers",
   "location": {
      "type":"Point",
      "coordinates":[35.5752569,10.2168173]
   }
}
```

A query that retrieves documents near a specific location is made like this

```
pois?_query={"location":{"$near":{"$geometry":{"type":"Point","coordinates":[35.530878,10.2354807]}, "$maxDistance": 500}}}
```
