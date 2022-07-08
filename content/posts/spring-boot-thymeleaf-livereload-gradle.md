+++
title = "Fixing the Spring Boot LiveReload Server with Gradle for Thymeleaf and TailwindCSS"
date = "2022-07-05T11:35:26+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["springboot", "thymeleaf", "gradle"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary = "I couldn't get Live Reload with Spring Boot and Thymeleaf to work. But I didn't want to use bulky node scripts. So I thought we can use Gradle"
+++


# LiveReloading with Thymeleaf

I couldn't get Live Reload with Spring Boot and Thymeleaf to work. The problem seems to be so widespread that even [@wimdeblauwe](https://twitter.com/wimdeblauwe) the author of Taming Thymeleaf uses gulp/npm to get Live Reloading working.

I thought why do we need to use Javascript to get our Spring Boot Developer Experience up to par with the big Javascript Frameworks? We have a perfectly fine Build Tool with Gradle!

## LiveReload.js

First we need to include a script tag which points to the LiveReload Server:

```html
<script src="http://localhost:35729/livereload.js"></script>
```
## Gradle Sync

The plan is the same as with the npm scripts. Sync the templates into the build output so LiveReload triggers properly. This is done with the Gradle Sync Task:


````kotlin
// build.gradle.kts
tasks.register("sync"){
	inputs.files("./src/main/resources/static","./src/main/resources/templates",)
	doLast {
		sync {
			from("./src/main/resources/static")
			into("build/resources/main/static")
		}
		sync {
			from("./src/main/resources/templates")
			into("build/resources/main/templates")
		}
	}
}
````

Now start a continuous build with:

````
.\gradlew -t sync
````

Each time one of the inputs.files directory changes the sync task is rerun and LiveReload works.

# Tailwind CSS

To use [TailwindCSS](https://tailwindcss.com/) we use the node-gradle plugin and create a npx task:
````kotlin
// build.gradle.kts
plugins {
    ...
    id("com.github.node-gradle.node") version "3.4.0"
}

tasks.register<com.github.gradle.node.npm.task.NpxTask>("tailwind"){
    command.set("tailwindcss")
    args.set(listOf("-i", "./src/main/resources/static/styles.css",  
        "-o" ,"./src/main/resources/static/output.css", "--watch"))
}
````

We then need to create a package.json:

````json
{
  "devDependencies": {
    "@tailwindcss/forms": "^0.5.2",
    "tailwindcss": "^3.1.4"
  }
}

````

Add a tailwind.config.js:

````javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{html,js}"],
}

````

Add a styles.css in resources/static/:
````css
@tailwind base;
@tailwind components;
@tailwind utilities;
````
and include output.css in our html:
 
````html
<link href="/output.css" rel="stylesheet">
````

Now we can start the tailwind npx task in a new terminal:

`````
.\gradlew tailwind
`````

And it works:

<video style="width: 100%" controls>
  <source src="https://i.imgur.com/YVudYpn.mp4" type="video/mp4">
</video>

You can see an example at: [github.com/tschuehly/thymeleaf-livereload-gradle](https://github.com/tschuehly/thymeleaf-livereload-gradle)
