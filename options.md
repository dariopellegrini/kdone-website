---
layout: default
title: Options API
parent: API
nav_order: 5
---

# Options API
Each `module` can be configured to have an API that describes other available APIs for that endpoint.  
It can be done by configuring `optionsEnabled` parameter in `module` configuration.
```kotlin
module<Game>("games") {

     ...
     
     optionsEnabled = true
     
     ...
     
}
```

This will open an `OPTION` API for the module.
