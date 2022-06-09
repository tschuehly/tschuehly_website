+++
title = "Creating a GO GUI with Alpine.js and Webview"
date = "2022-03-14T16:47:06+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["go", "alpinejs","webview","gui"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary = "There are a lot of options for building a [GUI](https://golangr.com/gui/) for Go applications. \n Coming from the web development world building the frontend with HTML seems as a no-brainer."
+++
There are a lot of options for building a [GUI](https://golangr.com/gui/) for Go applications.
Coming from the web development world building the frontend with HTML seems as a no-brainer.

## Webview
[Webview](https://github.com/webview/webview) is a tiny cross-platform library for C/C++/Golang to build modern cross-platform GUIs. The goal of the project is to create a common HTML5 UI abstraction layer for the most widely used platforms.

To start using webview you need to install webview:
`go get github.com/webview/webview`

On windows you need to have these two [dlls](https://github.com/webview/webview/tree/master/dll/x64) in the project root folder.

It supports two-way JavaScript bindings (to call JavaScript from C/C++/Go and to call C/C++/Go from JavaScript).
But writing pure javascript code for the interactivity (imo) is awful.

## Alpine.js to the rescue

"[Alpine.js](https://alpinejs.dev/) is a rugged, minimal tool for composing behavior directly in your markup." It fits perfectly for our usecase.


You can load alpine inline or from a file. The newest version is available at [unpkg.com/alpinejs](unpkg.com/alpinejs)

```go
func loadAlpine() string {
	return "paste alpine.js source here"
}
```



First you must initialize webview.
```go
func main() {
	webView := webview.New(true)
	defer webView.Destroy()

	webView.SetSize(600, 600, webview.HintNone)
	webView.Init(loadAlpine())
```

To execute go code with alpine we need to call webView.bind("functionName").

```go 
webView.Bind("extractSubDirectories", func(sourceFolder string) string {
  folderUrls = extractSubDirectories(sourceFolder)
    tmpl := template.Must(template.New("html").Parse(
      // language=GoTemplate
	`<div>
	  {{range $vendor, $folderDetailsArray := .}}
	    <div>
	      <h3>Vendor: {{$vendor}}</h2>
		{{range $folderDetails := $folderDetailsArray}}
		  <ul>
		    <li>{{ .Path }} filecount:: {{ .FileCount }}</li>
		  </ul>
		{{end}}
	    </div>
	  {{end}}
	 </div>`))
  var html bytes.Buffer
  err := tmpl.Execute(&html, folderUrls)
  if err != nil {
    logger.WritePrint("ERROR: " + err.Error())
  }
  return html.String()
})
```

To create your first page you call webView.Navigate() and supply it with your HTML. Then call webView.Run()

```html
webView.Navigate(`data:text/html` + `<!doctype html>
<html lang="de" x-data="{ pathInput: '', table : ''}">
    <body style="padding: 2rem">
		<h1>JPEG Sorter</h1>
		<p>Input the folder where the images are stored</p>
		<input type="text" x-model="pathInput"/>
				
		<button @click="table = ''; table = await extractSubDirectories(pathInput);">analyse folder</button>

		<div x-html=table></div>
    </body>
</html>`)
webView.Run()
```

## Alpine.js

As you can see there are quite a lot of non standard html attributes, this is the magic of alpine.js.
You can create local alpine data variables in the scope of the element:

```html
<html lang="de" x-data="{ pathInput: '', table : ''}">
```

You can bind input data to the local variables with x-model

```html
<input type="text" x-model="pathInput"/>
```

But the coolest part comes now. With an @click alpine attribute we can call our go functions from the html. The extractSubDirectories() function we binded earlier in this example.

```html
<button @click="table = await extractSubDirectories(pathInput);">
  analyse folder
</button>
```

With x-html we can bind the returned html from the go function into our gui.

```html
<div x-html=table></div>
```

These are the basic steps to get webview and alpine.js working with Go.

You can look at my recent freelance project for a complete example on [Github](https://github.com/tschuehly/pc3000-imagesorter/blob/master/webview.go).

