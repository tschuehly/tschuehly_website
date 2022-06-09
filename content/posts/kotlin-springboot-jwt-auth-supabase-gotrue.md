+++
title = "JWT authentication for Spring Boot simplified using GoTrue and Supabase"
date = "2022-03-01T16:51:45+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["kotlin", "springboot","auth","gotrue"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary = "In a quest to have a simpler JWT Authentication flow and not have to deal with security related userdata in my backend, I discovered Supabase Auth which is an implementation of Netlify GoTrue."
+++

In a quest to have a simpler JWT Authentication flow and not have to deal with security related userdata in my backend I discovered Supabase Auth which is an implementation of Netlify GoTrue.

For Kotlin there is the awesome supabase [gotrue-kt](https://github.com/supabase-community/gotrue-kt) library.

In your User Registration and Login Services you need to create a GoTrueClient
```kotlin
val goTrueClient = GoTrueClient.defaultGoTrueClient(
    url = "<base-url>",
    headers = mapOf("Authorization" to "foo", "apiKey" to "bar")
)
```

If you are using supabase, the base URL will be:
`https://<your-project-id>.supabase.co/auth/v1`

Then in your signup method you can just call signUpWithEmail().

```kotlin
val authDTO = goTrueClient()
    .signUpWithEmail(credentials["email"]!!, credentials["password"]!!)
websiteUserRepository.save(WebsiteUser(authDTO))
```

With the default client this returns a GoTrueUserResponse which most importantly contains a id which you then can persist in a WebsiteUser Authentication Pojo which holds information related to the user

With the goTrue Kotlin Library you can also specify a custom return type for example if you turned email confirmation off.

We define our DTO:
```kotlin
data class AuthDTO(
    val accessToken: String,
    val tokenType: String,
    val refreshToken: String,
    val user: User
)

data class User(
    val id: UUID,
    val email: String,
    val phone: String

)
```

and then create a Client where we pass this dto:

```kotlin
return GoTrueClient.customApacheJacksonGoTrueClient<AuthDTO, GoTrueTokenResponse>(url,headers)
```

In our Login Method we call signInWithEmail and then return the JWT from the GoTrue Response as Cookie

```kotlin
val repsonse = goTrueClient().signInWithEmail(
  credentials["email"],
  credentials["password"]
)
response.addCookie(
    Cookie("JWT", resp.accessToken).also {
        it.secure = true
        it.isHttpOnly = true
        it.path = "/"
        it.maxAge = 6000
    }
)
```

But we need to actually verify that the JWT is correct when a User request a page and that the user has the required access rights.

We do this in a JWT Filter that overrides the doFilterInternal method from the OncePerRequestFilter().

When our current SecurityContext Authentication is empty we need to extract the JWT from the Cookie and get the UserID from GoTrue to find the WebsiteUser we persisted earlier.
We then set the SecurityContext with the retrieved WebsiteUser

```kotlin
@Component
class JwtFilter(
    val websiteUserRepository: WebsiteUserRepository
) : OncePerRequestFilter() {
    override fun doFilterInternal(
        request: HttpServletRequest,
        response: HttpServletResponse,
        filterChain: FilterChain
    ) {
        if (SecurityContextHolder.getContext().authentication == null) {
            val auth = SecurityContextHolder.getContext()
            request.cookies?.find { it.name == "JWT" }?.let { cookie ->
                try {
                    goTrueClient.getUser(cookie.value).let {
                       SecurityContextHolder.getContext().authentication = websiteUserRepository.findByIdWithJPQL(it.id)
                    }
                } catch (e: GoTrueHttpException) {
                    if (e.data?.contains("Invalid token") == true) {
                        val oldCookie = request.cookies.find { it.name == "JWT" }
                        oldCookie?.maxAge = 0

                        response.addCookie(oldCookie)
                        response.sendRedirect("/")
                    }
                }
            }
        }

        filterChain.doFilter(request, response)
    }
}
```

At last we add this filter in our WebSecurityConfiguration:

```kotlin
@Configuration
@EnableWebSecurity(debug = false)
class SpringSecurityConfig(
    val jwtFilter: JwtFilter
) : WebSecurityConfigurerAdapter() {
    override fun configure(http: HttpSecurity) {
        http.formLogin()
            .loginPage("/login")
            .and()
            .logout()
            .deleteCookies("JWT", "authenticated")
            .logoutUrl("/logout")
            .logoutSuccessUrl("/")
            // Our private endpoints
            .antMatchers("/konto").hasRole("USER")
            .antMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
            .and()
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter::class.java)
    }
}
```
