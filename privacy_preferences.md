---
layout: default
title: Privacy preferences
parent: Users
nav_order: 8
---

# Privacy preferences

KDone lets to configure privacy consents that users can compile, read and update.

### Paragraphs
Privacy contents are made by paragraphs. Each paragraph can represent just text or a possible selection for the user, typically to express consent on a privacy aspect (i.e. terms and conditions consent).  
To activate privacy feature on a user, add privacy configuration inside `userModule` DSL.
```kotlin
privacy(
   paragraph("Privacy introduction", "This is a privacy introduction", "privacy_introduction"), 
   paragraph("Privacy 1", "This isa a selectable paragraph that must be responded true", "privacy1") {
        selectable = true
        mustBeAnswered = true
        mustBeAccepted = true
   },
   paragraph("Privacy 2", "This is a selectable paragraph that must be responded true or false", "privacy2") {
        selectable = true
        mustBeAnswered = true
        mustBeAccepted = false
   },
   paragraph("Privacy 3", "This isa a selectable paragraph that can be optionally responded true or false.", "privacy3") {
        selectable = true
        mustBeAnswered = false
        mustBeAccepted = false
   },
   paragraph("Privacy end", "End of privacy", "privacy_end")
)
```

### Endpoints
This configuration will open the endpoints necessary to let users to create, read and update their privacy preferences. All endpoints are authenticated by the user's token.
- `privacy/contents`, GET request that shows all paragraphs.
- `user/privacy`, POST request that creates privacy preferences. It returns an error if the user's privacy preferences already exist.  
The body required by this endpoint is a JSON array that contains users' responses for every selectable paragraph. Each response is identified by the paragraph key specified in the configuration.
```kotlin
{
   "preferences":[
      {
         "key":"privacy1",
         "accepted":true
      },
      {
         "key":"privacy2",
         "accepted":true
      },
      {
         "key":"privacy3",
         "accepted":true
      }
   ]
}
```
- `privacy/me`, PATCH which lets users update their privacy with the same body used with privacy preferences creation request.
- `privacy/me`, GET which gives the list of paragraphs with the current user responses.
- `user/privacy/filled`, GET which respond 204 if the user has already compiled privacy or 423 if the user has not compiled privacy yet.
