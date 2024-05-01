---
title: Flutter Chrome Extension - Part 2
date: 2024-4-30
status: published
summary: 'Excited to dive into the second part to creating a Chrome Extension with Flutter! This post will cover concepts related to dart-interop and help us understand build a more easy communication way'
link: https://dev.to/jamescardona11/flutter-chrome-extension-part-2-1kki
tags: [Flutter, Dart, Flutter-Web]
---

This post continues our journey to create a Chrome Extension with Flutter. In the last post, we created the base of the extension and we covered how to communicate between the extension components.

We have a pending goal in order to continue the summary using ChatGPT API, we want to summarize the selected text of the current page.


## The goal of the post

The goal of this post is based on the following points:
- Use dart interop to call native JS code and communicate between the extension components.
- Extends the knowledge on how to communicate between the extension components.


## Dart interop?

### What is dart interop?

This library facilitates smooth interaction between JavaScript (JS) and Dart by providing a comprehensive JS interop solution. It's important to note that the JS types defined in this library offer static guarantees only. This means that while they provide assurances at compile time, the actual types at runtime may vary depending on the backend being used. 

Dart interop is included in the Flutter SDK, so we don't need to add any dependency to use it and now we can use some JS-types to convert between Dart and JS.

### Why is it useful?

This allows us to call "JS" functions from the Dart code, note that we are not calling JS functions directly, we are calling the JS functions from the Dart code and we need to continue following the Chrome Extension rules to communicate between the components.

## index.js


Do you remember in the last post we created a function to get the current page URL?
We use something like this: using the `js` package to call the JS function from the Dart code.
```dart
@JS('chrome')
library main;

import 'package:js/js.dart';

@JS('tabs.query')
external Future<List<Tab>> query(ParameterQueryTabs parameterQueryTabs);
```



Now we are going to use the interop to call the JS function from the Dart code.
1) Create a new file `index.js` in the `web` folder.

```js
async function getPageUrl() {
  console.log("getPageUrl -- web/index.js");
  const tabs = await chrome.tabs.query({ 'active': true });
  console.log("Return from chrome.tabs.query", tabs[0].url);
  return tabs[0].url;
}
```

2) Create a dart file, in our example we called `raw_interop.dart`
Here we are going to put the definition of the JS function.

```dart
@JS()
library flutter_medellin_extension;

import 'dart:js_interop';

@JS()
external JSPromise<JSString> getPageUrl();
```

3) Create a second dart file, in our example, we called `js_interop.dart`
Here we are going to implement the call to the JS function.

```dart

import 'dart:js_interop';

import 'raw_interop.dart' as interop;

abstract class JsInterop {
  static Future<String> getPageUrl() async {
    return (await interop.getPageUrl().toDart).toDart;
  }
}
```

What is `toDart`? This is a method that is used to convert the JS type to a Dart type.
If you see the name of the dart function and JS function should be the same and is important to run JS code.


1) Use the `JsInterop` class to getPageUrl.

```dart
String pageUrl = await JsInterop.getPageUrl();
print('Page URL: $pageUrl');
```

## Implement the selected text function

In the index.js file, we are going to add a new function to get the selected text of the current page, but the problem is that we can't call `window` object, we need to execute this code in the background.js.

If you remember the way that we use in the latest post to communicate between components is sending messages, so we are going to use the same way to get the selected text.

To send a message to the background.js we are going to use the `chrome.runtime.sendMessage` also, we need to use the callback function to receive the return value, finally, we need to use a `Promise` to wait before we return the code to the Dart side.


We are going to write this in parts, first, we are going to write the JS code to get the selected text.

```js
async function getSelectedText() {
  console.log("selectedText -- web/index.js");
  chrome.runtime.sendMessage({ type: "selectedText" }, function (response) {});
}
```

Use a promise to resolve the response from the background.js and wait for the promise to return the selected text.

```js
const promise = new Promise(function (resolve, reject) {
  chrome.runtime.sendMessage({ type: "selectedText" }, function (response) {
    resolve(response);
  });
})

const selection = await promise;
```


Finally, the code looks like this:

```js
async function getSelectedText() {
  console.log("selectedText -- web/index.js");

  const promise = new Promise(function (resolve, reject) {
    chrome.runtime.sendMessage({ type: "selectedText" }, function (response) {
      resolve(response);
    });

  })

  const selection = await promise;
  if (selection) {
    return selection[0].result ?? '';
  }
  return '';
}
  
```

### Update the background.js

Here in the background, we are going to listen to the message, and also here we need to use the `Promise` to return the selected text.

```js
if (message.type === "selectedText") {
    const promise = new Promise(function (resolve, reject) {})

    promise.then((response) => {
      sendResponse(response);
    });
    return true;
  }
```

Two important things here:
- We can't use `await` it in the background.js, we need to use the `Promise` to wait for the response.
- We need to return `true` to indicate that we are going to use the `sendResponse` to send the response.

What is the code to get the selected text? (This code should be called inside the `Promise`)

```js
chrome.tabs.query({ active: true, currentWindow: true }, async function (tabs) {
  const tabId = tabs[0].id;
  const text = await chrome.scripting.executeScript({
    target: { tabId: tabId },
    function: () => getSelection().toString()
  });
  resolve(text);
});

```

### Call the JS function from Dart

Use the same way that we use to write and call the `getPageUrl` function.

**raw_interop.dart**

```dart
@JS()
external JSPromise<JSString> getSelectedText();
```


**js_interop.dart**
```dart
static Future<String> getSelectedText() async {
  return (await interop.getSelectedText().toDart).toDart;
}
```


**chrome_home_page.dart**

```dart
Future<void> _summarySelectedText() async {
  print('Summary Selected Text');

  String selectedText = await JsInterop.getSelectedText();
  print('Selected Text: $selectedText');

  setState(() {
    isLoading = true;
  });

  summary = await summaryApiClient.getTextSummary(selectedText) ?? 'Error fetching summary';

  setState(() {
    isLoading = false;
  });
}
```


### Result:

This is the result of the current page URL and the selected text.



This is a quick sneak peek at what other things we can do following this post and the previous post.



In this case, we are using the communication to the contentScript.js:
```js
const w = await chrome.windows.getCurrent()
  const tabs = await chrome.tabs.query({ active: true, windowId: w.id });
  chrome.tabs.sendMessage(tabs[0].id, { "type": "selectedText" }, function (response) {
    resolve(response);
  });
```


## What's next?

In this post, we covered how to use the Dart interop to call JS functions from the Dart code, and how to communicate between the extension components. I still have some pending goals to achieve using the extension. When I finish them, I will write something on my [LinkedIn](https://www.linkedin.com/in/jamescardona11/) or [Twitter](https://twitter.com/jamescardona11_).


## End words

Creating a Chrome Extension is a challenge due to the lack of documentation and API limitations, but it's good if you want to create something quickly without needing a lot of interaction with the HTML page.


Thank you for reading this far. Consider giving it a like, sharing it, and staying tuned for future articles. Feel free to contact me via [LinkedIn](https://www.linkedin.com/in/jamescardona11/).





### References
- https://zfinix.medium.com/building-a-chrome-extension-with-flutter-751e0674df09
- https://github.com/tmedanovic/XAI/tree/main
- https://medium.com/@joffrey.jougon/how-to-build-a-chrome-extension-on-flutter-and-use-the-chrome-api-62798f73c16f
- https://blog.langchaindart.com/langchain-dart-101-developing-an-llm-powered-summarizer-browser-extension-%EF%B8%8F-8b6cab84db69
- https://blog.stackademic.com/utilizing-js-library-for-flutter-web-c683c590927f
- https://medium.com/flutter-community/building-a-chrome-extension-using-flutter-aeb100a6d6c
- https://www.dhiwise.com/post/how-to-implement-flutter-js-package-in-your-flutter-apps
- https://stackoverflow.com/questions/41273787/getting-an-arbitrary-property-from-a-javascript-object-in-dart
- https://liewjuntung.medium.com/use-javascript-in-flutter-web-a6eed3efb9a0