---
layout: default
title: E-mail confirmation
parent: Users
nav_order: 5
---

# E-mail confirmation

KDone supports e-mail confirmation process after sign up. If configured, an e-mail is sent to the user after sign up.
This e-mail contains a link that confirms users' e-mail.

### STMP Client
In order to add the ability to send e-mail, it's necessary to configure an SMTP Client. KDone embeds an SMTP client that can be configured with host and credentials. Port is 465 by default.

```kotlin
val smtpClient = smtpClient(Email.host, Email.username, Email.password)
```

### E-mail confirmation configuration
E-mail configuration has these parameters:
- SMTP client
- Base URL of the webserver
- Redirect URL, to which user will be redirected to (optional)
- E-mail format configuration function, that lets to specify an e-mail format using the e-mail confirmation link provided by KDone.

Inside `userModule` DSL

```kotlin
emailConfirmation(
   emailSender = smtpClient,
   baseURL = Server.baseURL,
   redirectURL = "https://google.it") {
      link ->
         email(senderName = "Info",
            senderAddress = "no-reply@email.com",
            subject = "Confirm",
            message = """
               Use this link to confirm
               $link
            """.trimIndent())
      }
```

This configuration will send an e-mail after user signup, using username as the e-mail address.
The e-mail will contain a link like `https://site.com/users/auth/veryfy/<code>`, that, when opened, will confirm the user's e-mail address.

By default the e-mail confirmation is not mandatory to let users perform the login. It can be configured as mandatory in `userModule` DSL.

```kotlin
needsEmailConfirmation = true
```
