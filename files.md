---
layout: default
title: Files
nav_order: 4
---

# Files

KDone can manage files. To do that, 2 things are needed:

* Specify a `ResourceFile` in the model
* Configure an uploader in module configuration

First create a model
```kotlin
data class Game(
    val name: String,
    val players: Int?,
    val image: ResourceFile?): Identifiable()
```

Then in module configuration add an uploader
```kotlin
module<Game>("games") {
   uploder = fileUploader
}
```

In this case, `fileUploader` is a custom class that implements `Uploader` interface.  
This interface has 2 methods
1. `suspend fun save(modelName: String, fileName: String, file: File, contentType: String): String?`  
It is intended to add to the storage the uploaded file and it gets as input document's model name, file name, actual `File` object and content-type. As return value, it should have the file string URL.
2. `suspend fun delete(url: String)`  
It gets as input the file url and it is intended to delete that file from the storage.

### S3 uploader
S3 is a popular file container and strage system, used in a large amount of backends.  
In order to use that, KDone has a built-in S3 uploader.
```kotlin
module<Game>("games") {
   uploader = S3Uploader(
            baseFolder = S3.baseFolder,
            baseURL = S3.baseURL,
            bucketName = S3.bucketName,
            accessKey = S3.accessKey,
            secretKey = S3.secretKey,
            serviceEndpoint = S3.serviceEndpoint,
            signingRegion = S3.signingRegion
        )
}
```

### Local uploader
If it's not necessary to save files in a storage system, KDone has `localUploader` function, which configures a specific folder that will contain uploaded files and a GET request to retrieve them.
```kotlin
module<Game>("games") {
   uploader = localUploader("files-folder")
}
```

### Multipart request
File uploads are performed using POST and PATCH multipart requests.  
A ResourceFile object has in its JSON the information about URL and content-type
```kotlin
"image": {
    "url": "https://kdone.com/game/1578676851711aozI0SMf9C-Days-Gone-768x432.jpg",
    "mimeType": "image/jpeg"
  }
```
