+++
title = "Local development with Testcontainers and Kotlin"
date = "2022-07-08T13:23:37+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["kotlin", "springboot"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary = "It is always the best to keep your development environment as close as you can to your production environment. Don't use a h2 database as it can behave differently to a production database."
+++
It is always the best to keep your development environment as close as you can to your production environment. Don't use a h2 database as it can behave differently to a production database: [Issues mapping @Lob](http://localhost:1313/posts/spring-content/#issues)

Recently I found the article [Local development with Testcontainers](https://bsideup.github.io/posts/local_development_with_testcontainers/) by Sergei Egorov [@bsideup](https://twitter.com/bsideup).

I couldn't get it working with Kotlin, but after some help from the awesome twitter developer community I got it working.

Refactor your Application.kt, so you expose createSpringApplication as a static method:

````kotlin
@SpringBootApplication
class ExampleApplication{
    companion object {
        @JvmStatic
        fun createSpringApplication(): SpringApplication {
            return SpringApplication(ExampleApplication::class.java)
        }
    }

}

fun main(args: Array<String>) {
    runApplication<ExampleApplication>(*args)
}
````

Create a DevelopmentInitializer.kt in src/test/kotlin/com.example/DevelopmentInitializer.kt

````kotlin
@RunWith(SpringRunner::class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ContextConfiguration(initializers = [DevelopmentInitializer.Initializer::class])
abstract class DevelopmentInitializer {
    class Initializer : ApplicationContextInitializer<ConfigurableApplicationContext> {
        override fun initialize(context: ConfigurableApplicationContext) {
            val env = context.environment
            env.propertySources.addFirst(
                MapPropertySource(
                    "testcontainers", properties
                )
            )
        }

        companion object {
            private val postgresContainer = PostgreSQLContainer("postgres:14.4")
                .withUsername("postgres")
                .withPassword("password")
                .withDatabaseName("postgres")
                .withExposedPorts(5432)
                .withReuse(true)
            val properties: Map<String, String>
                get() {
                    Startables.deepStart(postgresContainer).join()
                    return mapOf(
                        "spring.datasource.url" to postgresContainer.jdbcUrl,
                        "spring.datasource.password" to postgresContainer.password,
                        "spring.datasource.username" to postgresContainer.username,
                    )
                }
        }
    }
}
````
To create reusable containers you need to set `testcontainers.reuse.enable=true` in `$HOME/.testcontainers.properties` file

Now create a DevelopmentApplication.kt in src/test/kotlin/com.example/DevelopmentApplication.kt

````kotlin
fun main(args: Array<String>) {
    val app = ExampleApplication.createSpringApplication()
    app.addInitializers(DevelopmentInitializer.Initializer())
    app.run(*args)
}
````

You can start DevelopmentApplication.main() to get your Spring Boot Application with an autoconfigured postgres container.

I created a working example at my GitHub: [github.com/tschuehly/testcontainers-localdev-kotlin](https://github.com/tschuehly/testcontainers-localdev-kotlin)
## Fixed Ports

You can also use a FixedHostPortContainer, then you can connect for example your IntelliJ Database Explorer and don't have to reconfigure it if you restart your Docker Environment

````kotlin
val postgresContainer = FixedHostPortGenericContainer("postgres:14.4")
    .withEnv("POSTGRES_USER","postgres")
    .withEnv("POSTGRES_PASSWORD","password")
    .withEnv("POSTGRES_DB","postgres")
    .withFixedExposedPort(5432,5432)
    .withReuse(true)

val properties: Map<String, String>
    get(){
        Startables.deepStart(postgresContainer, minioContainer).join()
        return mapOf(
            "spring.datasource.url" to 
                    "jdbc:postgresql://" + postgresContainer.host + ":5432/postgres",
            "spring.datasource.password" to "password",
            "spring.datasource.username" to "postgres",
    )
}
````
