---
layout: default
title: One-time password
parent: Users
nav_order: 7
---

# One-time password

KDone supports OTP (One-time password).

### Configure one or more senders
An OTP systems needs a system capable to send access codes to users. To create one, simply create a class which implements `UserSender` interface. This interface requires just a method which takes as argument the user and the OTP code. In its implementation should be added the sending logic.

```kotlin
class SMSSender(val smsProviderKey: String): UserSender<User> {
    override suspend fun send(user: User, message: String) {
        SMSProvider.send(message, user.number, smsProviderKey)
    }
}
```

### Add OTP to KDone configuration
With at least a sender it's possible to activate OTP simply doing
```kotlin
val senderSMS = SMSSender("SMSSenderKey")
val senderEmail = EmailSender()

otp(senderSMS, senderEmail)
```

### Endpoint
After this configuration, OTP endpoints will be opened.

* `users/otp/access`, POST which takes as input user's parameters (JSON or Multipart Form Data). This endpoint will create the user if not already exists and send the OTP access code though the senders specified in OTP configuration.
* `users/otp/access/registered`, POST which sends the OTP through senders and requires a JSON body with username value. If username does not exists returns an error.
* `users/otp/login`, POST which let users login using username and OTP code. They should be sent in a JSON inside request's body.
```json
{
	"username":"info@email.com",
	"password":"ykbVjS"
}
```

### Code generation
OTP configuration accept a function in order to generate a OTP code.
```kotlin
otp(otpSender,
      codeGenerator = { randomString(6) }
)
```

If not specified a codeGenerator function, KDone will generate a 5 characters random string as OTP code.

### E-mail sender
KDone has a build in Email sender to send OTP codes through e-mail. It takes as arguments a [SMTP Client](https://dariopellegrini.github.io/kdone-website/email_confirmation.html#stmp-client), sender info of the sent e-mail, object of the message and a closure to compose and send the e-mail content using generated OTP code.

```kotlin
val otpSender = EmailSender<User>(smtpClient, "Info" to "info@company.com", "One Time Password") {
                    code ->
                """
                    Il codice di conferma per accedere è
                    $code
                """.trimIndent()
            }

otp(otpSender)
```
