---
layout: default
title: Password recovery
parent: Users
nav_order: 6
---

# Password recovery

Similar to [E-mail confirmation](email_confirmation.md), KDone supports password recovery.

The logic is pretty straightforward.
- User calls an API with e-mail and the new desired password.
- User receives a confirmation link that should be clicked to change the password.
- Once the user clicks the link the password will be changed.

Add password recovery configuration inside `userModule` DSL.
```kotlin
passwordRecovery(emailClient = smtpClient,
                baseURL = Server.baseURL,
                redirectURL = "https://google.it") {
                    link ->
                email("Info",
                    "info@dariopellegrini.com",
                    "Password recovery",
                    """
                    Use this link to confirm new password.
                    $link
                """.trimIndent())
            }
```

Password recovery configuration takes as parameters
- SMTP client
- Base URL of web server
- Redirect URL, to which user will be redirected to (optional)
- E-mail format configuration function, that lets to specify an e-mail format that uses password recovery link provided by KDone.

This configuration will open `users/password/recovery` POST endpoint, which needs a body containing the user's e-mail and a new password.  
```json
{
	"email":"info@email.com",
	"newPassword":"password"
}
```

After that, a link will be sent to that e-mail address. Once clicked, the password will be changed.
