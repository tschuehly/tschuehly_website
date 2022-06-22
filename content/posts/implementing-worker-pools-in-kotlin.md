+++
title = "Implementing Worker Pools in Kotlin to upload Images over WebDav to a Hetzner StorageBox"
date = "2022-06-22T18:04:34+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["kotlin", "springboot","webdav","hetzner"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary="Recently while doing a project with Go I came across Worker Pools on GoByExample to do parallel processing. I didn’t find many resources for implementing Worker Pools in Kotlin, but it seemed a good idea for my current Spring Boot + Kotlin webapp."
+++
# Worker pools
Recently while doing a project with Go I came across Worker Pools on [GoByExample](https://gobyexample.com/worker-pools) to do parallel processing. I didn't find many resources for implementing Worker Pools in Kotlin, but it seemed a good idea for my current Spring Boot + Kotlin webapp.

# Kotlin
Kotlin uses coroutines for concurrency which are fairly similiar to goroutines.

Coroutines use structured concurrency to delimit the lifetime of each coroutine to a certain scope.

To be able to create a workergroup we need to create a coroutinescope that is persistent over the livetime of our application. We achieve this behaviour with the SupervisorJob() context.

```kotlin
private val coroutineScope = CoroutineScope(SupervisorJob() + Dispatchers.Default)
```

We then create a buffered channel as queue for our image data and the url where we want to upload it.

```kotlin
val channel = Channel<Pair<String, ByteArray>>(10000)
```

I'm using the Spring @PostConstruct annotation to create the worker group and listen to the channel for new data.
Each time a item is in the queue we launch the upload function, if no item is in the queue the function is suspended.

```kotlin
@PostConstruct
    fun createWorkerGroup() {
        coroutineScope.launch {
            for (x in 1..5) {
                launch {
                    println("Create Worker $x")
                    while (true) {
                        uploadImage(channel.receive())
                    }
                }
            }
        }
    }
```

Finally we can send our data to our channel inside a runBlocking coroutine scope:

```kotlin
runBlocking {
  uploadService.channel.send(Pair(url, image.bytes))
}
```


# WebDav

In my webapp users upload images from their mobile phone to my webserver, afterwards I want to upload these pictures to a [Hetzner Storage Box](https://www.hetzner.com/storage/storage-box) over webdav as a cheap alternative to an S3 object storage.

I use the [sardine](https://github.com/lookfirst/sardine) java webdav client library for its simplicity.

The usage is very straightforward, you configure the client with:

```kotlin
val sardine = SardineFactory.begin("webDavUsername", "webDavPassword")
```

The uploadImage Function is called every time a new image is sent over the channel we created earlier. In this function we call sarding.put() to save the image file.

```kotlin
sardine.put("https://username.your-storagebox.de/foldername/imagename.jpg", ImageByteArray)
```

That is all we need to have a highly parallel File Service

You can view the Sourcecode at Github: [UploadService.kt](https://github.com/tschuehly/weddingGame/blob/master/src/main/kotlin/de/tschuehly/weddingGame/service/UploadService.kt#L40=) [ImageService.kt](https://github.com/tschuehly/weddingGame/blob/512ace07fa1d0a57253b63bbb5cbf5ab3880e151/src/main/kotlin/de/tschuehly/weddingGame/service/ImageService.kt#L19)



