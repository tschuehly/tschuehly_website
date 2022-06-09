+++
title = "Spring Content"
date = "2021-11-18T16:34:21+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["kotlin", "java","jpa","springboot"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary = "The better way to save and serve files and images with Spring Boot."
+++

# Save images directly into the DB <a name="save-image"></a>

Recently I added a feature to my web app where my customer could upload images and display them inside the app.

At first I stored them directly into the db as ByteArray inside an JPA Entity with a @Lob annotation

```kotlin
@Entity
class Picture(
    val name: String?,
    val type: String?,
    @Lob
    val data: ByteArray
)
```

I then returned them as Byte64 encoded string inside my JSON API Response.

### Issues

It was working fine in my local dev enviroment, but as soon as I pushed it to prod I encountered an error where my whole json structure needed for my frontend wasn't displayed properly.
The Byte64 encoded image string just stopped in the middle.

I cloned the prod db locally into a postgres container and then the issue occured also locally.

HHH000100: Fail-safe cleanup (collections) : org.hibernate.engine.loading.internal.CollectionLoadContext@2555100f<rs=HikariProxyResultSet@1189576917 wrapping org.postgresql.jdbc.PgResultSet@545635a4>


After some 2am debugging I got a JacksonMapping exception and found the error on [stackoverflow](https://stackoverflow.com/questions/54013834/could-not-write-json-unable-to-access-lob-stream).

I thought the solution was too complex and looked for a simpler way.

### Learnings

1. Lessons: Always test with an environment as similiar as production. I can recommend [testcontainers](https://www.testcontainers.org/) for easier integration tests.

2. Lesson: Use a database versioning tool such as liquibase instead of spring.jpa.hibernate.ddl-auto = update.

With h2 the @Lob was correctly saved as bytearray. With postgres the type was assumed to be oid which caused a issue mapping between @Lob and the bytea column type.

This was fixed with the hibernate Annotation

`@Type(type = "org.hibernate.type.MaterializedBlobType")`

3. Lesson: Dont serve images directly as Base64 encoded string.

Instead use a seperate controller and serve the imagedata raw.

Serialize the entity which stores the data and the metadata.

```kotlin
@JsonIdentityInfo(generator = ObjectIdGenerators.PropertyGenerator::class, property = "id")
@JsonIdentityReference(alwaysAsId=true)
```

This avoids huge json responses and slowdowns to your website.

# Spring Content to the rescue <a name="spring-content"></a>

Then I discovered [spring-content](https://github.com/paulcwarren/spring-content) which looked like a better alternative than saving them directly to a db, as I could switch between db, filesystem, AWS S3 and MongoDB GridFs by just switching the dependency.


To use Spring Content you just need to add the depencency in your build.gradle.kts

```kotlin
implementation("com.github.paulcwarren:content-jpa-spring-boot-starter:1.2.5")
```

Add the @ContentId annotation to your file entity. In my case i replaced:

```kotlin
    @JsonIgnore
    @Type(type = "org.hibernate.type.MaterializedBlobType")
    val data: ByteArray
```

with


```kotlin
@ContentId var imageId: String
```

Create a ContentStore

```kotlin
interface PictureContentStore : ContentStore<Picture, String>
```

In your method where you save the Files you need to use the contentstore.setContent() method. It is important to first save the entity.


```kotlin
val picture = pictureRepository.save(Picture(pictureFile.originalFilename, pictureFile.contentType,null))
pictureContentStore.setContent(picture, pictureFile.inputStream)
pictureRepository.save(picture)
```

Add the handling of pictures to your file entity
```kotlin
@RestController
@RequestMapping("api/picture")
class PictureController(
    val pictureService: PictureService,
    val pictureContentStore: PictureContentStore,
) {
    @GetMapping("/{id}")
    fun getById(
        @PathVariable id: Long,
    ): ResponseEntity<InputStreamResource> {
        pictureService.getById(id)
            ?.let { pic ->
                val content = pictureContentStore.getContent(pic)
                val inputStreamResource = InputStreamResource(content)

                return ResponseEntity.ok()
                    .contentType(MediaType.parseMediaType(pic.type.toString()))
                    .body(inputStreamResource)
            }
        throw NoSuchElementException("There is no Image with the corresponding id")
    }
}

```

and you are done.

You can look up my source code on [Github](https://github.com/tschuehly/DataRecovery).

This is my first project as a freelancer. Any suggestions or feedback is very welcome
