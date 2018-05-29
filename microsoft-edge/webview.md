---
description: Host web content in your Windows 10 app with the Microsoft Edge WebView control
title: Microsoft Edge WebView for Windows 10 apps
author: libbymc
ms.author: libbymc
ms.date: 4/10/2018
ms.topic: reference
ms.prod: microsoft-edge
keywords: x-ms-webview, MSHTMLWebViewElement, webview, windows 10 apps, uwp, edge
---

# Microsoft Edge WebView for Windows 10 apps

The Microsoft Edge WebView control enables you to host web content in your Windows 10 app. You can use it as a [XAML element](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.WebView) (for C# and C++ Windows 10 apps, [Windows Forms and WPF desktop applications](https://docs.microsoft.com/en-us/windows/uwpcommunitytoolkit/controls/webview)), or an HTML element (`<x-ms-webview>`)/DOM object (`MSHTMLWebViewElement`) for JavaScript-based Windows 10 apps, as described here.

| | |
|-|-|
| [**Events**](#events) | [departingFocus](#departingfocus), [MSWebViewContainsFullScreenElementChanged](#mswebviewcontainsfullscreenelementchanged), [MSWebViewContentLoading](#mswebviewcontentloading), [MSWebViewDOMContentLoaded](#mswebviewdomcontentloaded), [MSWebViewFrameContentLoading](#mswebviewframecontentloading), [MSWebViewFrameDOMContentLoaded](#mswebviewframedomcontentloaded), [MSWebViewFrameNavigationCompleted](#mswebviewframenavigationcompleted), [MSWebViewFrameNavigationStarting](#mswebviewframenavigationstarting), [MSWebViewLongRunningScriptDetected](#mswebviewlongrunningscriptdetected), [MSWebViewNavigationCompleted](#mswebviewnavigationcompleted), [MSWebViewNavigationStarting](#mswebviewnavigationstarting), [MSWebViewNewWindowRequested](#mswebviewnewwindowrequested), [MSWebViewPermissionRequested](#mswebviewpermissionrequested), [MSWebViewProcessExited](#mswebviewprocessexited), [MSWebViewWebResourceRequested](#mswebviewwebresourcerequested), [MSWebViewScriptNotify](#mswebviewscriptnotify), [MSWebViewUnsafeContentWarningDisplaying](#mswebviewunsafecontentwarningdisplaying), [MSWebViewUnsupportedUriSchemeIdentified](#mswebviewunsupportedurischemeidentified), [MSWebViewUnviewableContentIdentified](#mswebviewunviewablecontentidentified)
| [**Methods**](#methods) | [addWebAllowedObject](#addweballowedobject), [buildlocalStreamUri](#buildlocalstreamuri), [capturePreviewToBlobAsync](#capturepreviewtoblobasync), [captureSelectedContentToDataPackageAsync](#captureselectedcontenttodatapackageasync), [invokeScriptAsync](#invokescriptasync), [getDeferredPermissionRequests](#getdeferredpermissionrequests), [getDeferredPermissionRequestById](#getdeferredpermissionrequestbyid), [goBack](#goback), [goForward](#goforward), [navigate](#navigate), [navigateFocus](#navigatefocus), [navigateTolocalStreamUri](#navigatetolocalstreamuri), [navigateToString](#navigatetostring), [navigateWithHttpRequestMessage](#navigatewithhttprequestmessage), [refresh](#refresh), [stop](#stop) |
| [**Properties**](#properties) | [canGoBack](#cangoback), [canGoForward](#cangoforward), [containsFullScreenElement](#containsfullscreenelement), [documentTitle](#documenttitle), [height](#height), [process](#process), [settings](#settings), [src](#src), [width](#width) |

## Syntax

```js
var webview = document.createElement("x-ms-webview"); 
```
## Remarks
When a Windows app using JavaScript is printed, the `<x-ms-webview>` tags are transformed into `<iframe>` tags before printing. Besides the normal difference between displaying on screen and rendered for print, CSS styles applied to `<iframe>` elements are then applicable to the `<iframe>` transformed from `<x-ms-webview>`. 

Creating a WebView via `document.createElement("x-ms-webview")` or via `<x-ms-webview>` markup creates a WebView on a new unique thread in the app's process. Running on a new unique thread means that long running script from one WebView is unable to hang the app or other WebViews. Creating a WebView via the `new MSWebView()` constructor creates a WebView in a separate WebView process. Running in a unique process means that in addition to protection from long running script, the app is also protected from web content that crashes the WebView process. Creating a WebView via the [`MSWebViewProcess.createWebViewAsync`](./webview/MSWebViewProcess.md#createwebviewasync) method also creates a WebView in a seperate process but allows the caller more control over process settings and grouping WebViews in WebView processes. See `MSWebViewProcess` for more information. 

For more information, see the [HTML WebView control sample](http://go.microsoft.com/fwlink/p/?linkid=309825).

### AppCache storage limitations
Applications using JavaScript support of the Application Cache API (or AppCache), as defined in the [HTML5 specification](http://go.microsoft.com/fwlink/p/?LinkId=228542), to create offline web applications must observe available storage limitations. This is especially true in devices with limited memory space. The practical limits on the size of the AppCache are always a function of available disk storage space. The general guidelines are shown below.

| Volume size         |AppCache per domain | AppCache per user   | 
|---------------|---------------|-------------------------|
Up to 4GB | 10MB | 50MB |
4GB to 16GB | 50MB | 500MB | 
16GB to 32 GB | 50MB | 1GB |

All Windows 10 apps are intended to use the same AppCache quota model, so the available disk storage limitation applies to both desktop and phone apps. The is also a hard limit after pages loaded inside **WebView** together have consumed 1 GB of *AppCache* space; requests for additional *AppCache* storage above this limit will be denied. 

## Events

### departingFocus

Indicates focus departing from the **webview** into the app. Fires when the webview's top level document calls window.departFocus. The FocusNavigationEvent args in the departingFocus event match the parameters provided to departFocus except the origin parameters are translated from the webview's document's coordinate space to the coordinate space of the webview host document. See the webview.navigateFocus method and corresponding window navigatingFocus event for transferring focus from host to webview. See the [TVJS's Direction Navigation library](https://github.com/Microsoft/TVHelpers/wiki/Using-DirectionalNavigation) for an example of an implementation of focus navigation via keyboard or gamepad that handles this event.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("departingFocus", handler);
webview.removeEventListener("departingFocus", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [FocusNavigationEvent](./webview/FocusNavigationEvent.md) |
|**Synchronous** |Yes |    
|**Bubbles**     |Yes |   
|**Cancelable**  |No |            

### MSWebViewContainsFullScreenElementChanged

Occurs when the status changes of whether or not the **webview** currently contains a full screen element. Use the containsFullScreenElement property to the current value. Full screen element here refers to the [Fullscreen DOM APIs](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API) notion of a full screen element via the element.requestFullScreen and document.exitFullScreen DOM functions.

```js
function containsFullScreenElementChangedHandler(eventInfo) {
    const applicationView = Windows.UI.ViewManagement.ApplicationView.getForCurrentView();
    if (webview.containsFullScreenElement) {
        webview.classList.add("fullscreen"); // Have the webview fill the app view
        applicationView.tryEnterFullScreenMode(); // Have the app view fill the screen
    }
    else {
        webview.classList.remove("fullscreen"); // Return webview to normal
        applicationView.exitFullScreenMode(); // Return app view to normal
    }
}
 
// addEventListener syntax
webview.addEventListener("MSWebViewContainsFullScreenElementChanged", containsFullScreenElementChangedHandler);
webview.removeEventListener("MSWebViewContainsFullScreenElementChanged", containsFullScreenElementChangedHandler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | **Event** |
|**Synchronous** |Yes |    
|**Bubbles**     |No |   
|**Cancelable**  |No |  

### MSWebViewContentLoading

Indicates that the HTML content is downloaded and is being loaded into the control.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewContentLoading", handler);
webview.removeEventListener("MSWebViewContentLoading", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |    

### MSWebViewDOMContentLoaded

Indicates that the main DOM elements have finished loading.


```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewDOMContentLoaded", handler);
webview.removeEventListener("MSWebViewDOMContentLoaded", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |                 

### MSWebViewFrameContentLoading

Indicates that the HTML content downloaded and is being loaded into the `<iframe>` control.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewFrameContentLoading", handler);
webview.removeEventListener("MSWebViewFrameContentLoading", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |                 

### MSWebViewFrameDOMContentLoaded

Indicates that the main DOM elements have finished loading in the `<iframe>`.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewFrameDOMContentLoaded", handler);
webview.removeEventListener("MSWebViewFrameDOMContentLoaded", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |    


### MSWebViewFrameNavigationCompleted

Indicates the navigation is complete, and all media elements are rendered in the `<iframe>`.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewFrameNavigationCompleted", handler);
webview.removeEventListener("MSWebViewFrameNavigationCompleted", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationCompletedEvent](./webview/NavigationCompletedEvent.md) |
|**Synchronous** |Yes |    
|**Bubbles**     |No |   
|**Cancelable**  |No |       

### MSWebViewFrameNavigationStarting

Indicates a `<iframe>` within a **webview** is starting to navigate. This is fired before obtaining any resources from the network for the navigation. The navigation is not started until all MSWebViewFrameNavigationStarting event handlers complete. This event is cancellable via calling `eventArgs.preventDefault()`. If cancelled, the WebView will not perform the navigation.


```js
function frameNavigationStartingHandler(navigationEventArgs) {
    // Cancel all navigations that don't meet some criteria.
    if (!navigationEventArgs.uri.startsWith("https://example.com/")) {
        navigationEventArgs.preventDefault();
    }
}
 
// addEventListener syntax
webview.addEventListener("MSWebViewFrameNavigationStarting", frameNavigationStartingHandler);
webview.removeEventListener("MSWebViewFrameNavigationStarting", frameNavigationStartingHandler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |Yes |                 
          
### MSWebViewLongRunningScriptDetected

Occurs periodically during uninterrupted script execution in the **webview**, letting you halt the script.

```js
function handler(eventInfo) {
    const stopPageScriptThreshold = 5 * 1000;
    if (eventInfo.executionTime > stopPageScriptThreshold) {
        eventInfo.stopPageScriptExecution = true; // Stop the long running script if it goes over our threshold
    }
}
 
// addEventListener syntax
webview.addEventListener("MSWebViewLongRunningScriptDetected", handler);
webview.removeEventListener("MSWebViewLongRunningScriptDetected", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [LongRunningScriptDetectedEvent](./webview/LongRunningScriptDetectedEvent.md) |
|**Synchronous** |Yes |    
|**Bubbles**     |No |   
|**Cancelable**  |No |            

### MSWebViewNavigationCompleted

Indicates the navigation is complete, and all media elements are rendered.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewNavigationCompleted", handler);
webview.removeEventListener("MSWebViewNavigationCompleted", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationCompletedEvent](./webview/NavigationCompletedEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |                 
         

### MSWebViewNavigationStarting

Indicates the **webview** is starting to navigate. This is fired before obtaining any resources from the network for the navigation. The navigation is not started until all MSWebViewNavigationStarting event handlers complete. This event is cancellable via calling `eventArgs.preventDefault()`. If cancelled, the WebView will not perform the navigation.


```js
function navigationStartingHandler(navigationEventArgs) {
    // Cancel all navigations that don't meet some criteria.
    if (!navigationEventArgs.uri.startsWith("https://example.com/")) {
        navigationEventArgs.preventDefault();
    }
}
 
// addEventListener syntax
webview.addEventListener("MSWebViewNavigationStarting", navigationStartingHandler);
webview.removeEventListener("MSWebViewNavigationStarting", navigationStartingHandler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |No  |    
|**Bubbles**     |Yes |   
|**Cancelable**  |Yes |      

### MSWebViewNewWindowRequested

Raised when content in **webview** is trying to open a new window. If the event isn't cancelled the webview will launch the URI of the new window request in the user's default browser.

```js
function handler(eventInfo) {
    // Prevent the webview from opening URIs in the default browser.
    eventInfo.preventDefault();
    
    // Only allow https://example.com/ to open new windows.
    if (eventInfo.referer === "https://example.com/") {
        // Perform some custom handling of the URI.
        openUri(eventInfo.uri);
    }
}
 
// addEventListener syntax
webview.addEventListener("MSWebViewNewWindowRequested", handler);
webview.removeEventListener("MSWebViewNewWindowRequested", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEventWithReferrer](./webview/NavigationEventWithReferrer.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |Yes |                 
           

### MSWebViewPermissionRequested

Indicates that content in the **webview**  is trying to access functionality (such as geolocation, or pointer lock access) that normally requires end-user permissions.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewPermissionRequested", handler);
webview.removeEventListener("MSWebViewPermissionRequested", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [PermissionRequest](./webview/PermissionRequest.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |    


### MSWebViewProcessExited

Indicates that the **webview** component process crashsed. This is only relevant for an out-of-process WebView. See the Remarks section for how to create an out-of-process WebView as opposed to an in-process WebView. After this event fires, the corresponding WebView is put into a crashed state. Calling most WebView specific methods or accessing most WebView specific properties on a crashed WebView will throw an exception. To recover from this event, remove the crashed WebView from the DOM and replace it with a new WebView.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewProcessExited", handler);
webview.removeEventListener("MSWebViewProcessExited", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | **Event** |
|**Synchronous** |Yes |    
|**Bubbles**     |No |   
|**Cancelable**  |No |      


### MSWebViewWebResourceRequested

Raised when a page inside the **webview** element requests a resource.

```js
// A handler that completes synchronously with a custom HTTP response built from a string.
function handlerWithSyncString(webResourceRequestedEventArgs) {
    // Only provide custom HTTP responses for particular HTTP requests
    if (webResourceRequestedEventArgs.args.request.requestUri.absoluteUri === "https://www.bing.com/") {
        const Http = Windows.Web.Http;
        // Create the custom response using a string
        webResourceRequestedEventArgs.args.response = new Http.HttpResponseMessage(Http.HttpStatusCode.ok);
        webResourceRequestedEventArgs.args.response.content = new Http.HttpStringContent("<H1>Example</H1>");
    }
});

// A more complicated handler that completes asynchronously with a custom HTTP response built from a stream.
function handlerWithAsyncStream(webResourceRequestedEventArgs) {
    // Only provide custom HTTP responses for particular HTTP requests
    if (webResourceRequestedEventArgs.args.request.requestUri.absoluteUri === "https://www.bing.com/") {
        // Take a deferral on the WebResourceRequested event so that we can create the custom HTTP response asynchronously.
        const deferral = webResourceRequestedEventArgs.args.getDeferral();

        // Use fetch to get a Blob of the content of the URI
        fetch("ms-appx:///replacement.html").then(fetchResponse => {
            return fetchResponse.blob();
        }).then(fetchResponseBlob => {
            const Http = Windows.Web.Http;
            webResourceRequestedEventArgs.args.response = new Http.HttpResponseMessage(
                Http.HttpStatusCode.ok);

            // Use Blob.msDetachStream to convert the Blob to a Windows.Storage.Streams.IInputStream
            webResourceRequestedEventArgs.args.response.content = new Http.HttpStreamContent(
                fetchResponseBlob.msDetachStream());

        }).finally(() => {
            // Use a finally call to complete the deferral so even if there is an error we will unblock the event.
            deferral.complete();
        });
    }
});
 
// addEventListener syntax
webview.addEventListener("MSWebViewWebResourceRequested", handler);
webview.removeEventListener("MSWebViewWebResourceRequested", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [WebResourceRequestedEvent](./webview/WebResourceRequestedEvent.md) |
|**Synchronous** |Yes |    
|**Bubbles**     |No |   
|**Cancelable**  |No |      


### MSWebViewScriptNotify

Raised when a page inside the **webview** element calls the **window.external.notify** method. The window.external.notify method is only available to documents with URIs that match rules in the app's manifest's ApplicationContentUriRules or that has been programatically allowed via setting webview.settings.isScriptNotifyAllowed to true. Additionally window.external.notify is only available to the webview's top level document.

```js
webview.addEventListener("MSWebViewScriptNotify", eventInfo => {
    console.log("The URI " + eventInfo.callingUri + 
        " has sent notification " + eventInfo.value);
});

// Allow the next URI to which we navigate access to window.external.notify
webview.settings.isScriptNotifyAllowed = true;
webview.navigate("https://example.com/");

webview.addEventListener("MSWebViewNavigationCompleted", () => {
    // Inject script to the webview that will send a notification back.
    const asyncOp = webview.invokeScriptAsync("eval", "window.external.notify('example notification')");
    asyncOp.start();
});
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [ScriptNotifyEvent](./webview/ScriptNotifyEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |      

### MSWebViewUnsafeContentWarningDisplaying

Occurs when the **webview** shows a warning page for content that was reported as unsafe by SmartScreen Filter.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewUnsafeContentWarningDisplaying", handler);
webview.removeEventListener("MSWebViewUnsafeContentWarningDisplaying", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | **Event** |
|**Synchronous** |Yes |    
|**Bubbles**     |No |   
|**Cancelable**  |No |    

### MSWebViewUnsupportedUriSchemeIdentified

Raised when there is navigation to an Uniform Resource Identifier (URI) that the **webview** does not support.

```js
function handler(eventInfo) { /* Your code */ }
 
// addEventListener syntax
webview.addEventListener("MSWebViewUnsupportedUriSchemeIdentified", handler);
webview.removeEventListener("MSWebViewUnsupportedUriSchemeIdentified", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [NavigationEvent](./webview/NavigationEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |Yes |     

### MSWebViewUnviewableContentIdentified

Raised when the **webview** attempts to navigate to web content with an unsupported content type. For example, PDFs are currently not supported by webview and navigations to PDF documents will not navigate and instead raise this event.

```js
function handler(args) {
    if (args.mediaType === "application/pdf") {
        Windows.System.Launcher.launchUriAsync(new Windows.Foundation.Uri(args.uri));
    }
}

// addEventListener syntax
webview.addEventListener("MSWebViewUnviewableContentIdentified", handler);
webview.removeEventListener("MSWebViewUnviewableContentIdentified", handler);
```

#### Event Information

|            |      |
|------------|------|
|**Interface** | [UnviewableContentIdentifiedEvent](./webview/UnviewableContentIdentifiedEvent.md) |
|**Synchronous** |Yes  |    
|**Bubbles**     |No |   
|**Cancelable**  |No |                 
         

## Methods

### addWebAllowedObject

Adds a native Windows Runtime object as a global parameter to the top-level document inside of a **webview**.


```js
webview.addWebAllowedObject(name, applicationObject);
```
#### Parameters
*name*
* Type: **String**
* The name of the object to expose to the document in the **webview**.

*applicationObject*
* Type: **Object**
* The object to expose to the document in the **webview**.

#### Return value
This method does not return a value.

#### Additional features and requirements

|            |      |
|------------|------|
|**Minimum supported client** |Windows 10 [Windows Store apps only] |    
|**Minimum supported server** |None supported |   
|**Minimum supported phone**  |None supported |  

### buildLocalStreamUri

Creates a URI that you can pass to [navigateToLocalStreamUri](#methods).

```js
var string = webview.buildLocalStreamUri(contentIdentifier, relativePath);
```
#### Parameters
*contentIdentifier*
* Type: **String**
* A unique identifier for the content the URI is referencing. This defines the root of the URI.

*relativePath*
* Type: **String**
* The path to the resource, relative to the root.

#### Return value
Type: **String**

The URI created by combining and normalizing the *contentIdentifier* and *relativePath*.

#### Example

The following example illustrates a typical use case. 

```js
var webview = document.createElement("x-ms-webview"); //Instantiate the webview element
var localStreamUri = webview.buildLocalStreamUri(contentIdentifier, relativePath); //Create URI to pass to navigateToLocalStreamUri method
webview.navigateToLocalStreamUri(localStreamUri, streamResolver); //Load the local web content	
```  

### capturePreviewToBlobAsync

Creates an image of the current [webview element](./webview.md) and write it to the specified image element.

```js
var capturePreviewToBlobAsync = webview.capturePreviewToBlobAsync();
```
#### Parameters
This method has no parameters.

#### Return value
Type: **MSWebViewAsyncOperation**

An **MSWebViewAsyncOperation** object that, when it completes, provides a **Blob** object that contains the image. When using **capturePreviewToBlobAsync**, you need to define success and error handlers after defining the operation. After applying the event handlers, call the start method on the [MSWebViewAsyncOperation](./webview/MSWebViewAsyncOperation.md) object to execute the operation.

### captureSelectedContentToDataPackageAsync

Asynchronously gets a [DataPackage](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage) that contains the selected content within the **webview**.

```js
var msWebViewAsyncOperation = webview.captureSelectedContentToDataPackageAsync();
```
#### Parameters
This method has no parameters.

#### Return value
Type: **MSWebViewAsyncOperation**

An **MSWebViewAsyncOperation** object that, when it completes, provides a [DataPackage](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage) object that contains the image. When using **captureSelectedContentToDataPackageAsync**, you need to define success and error handlers after defining the operation. After applying the event handlers, call the start method on the MSWebViewAsyncOperation object to execute the operation.

```js
 var operation = webview.captureSelectedContentToDataPackageAsync();
    operation.oncomplete = function () {
        // operation.result is a package object that contains the selected data.
        var url = URL.createObjectURL(operation.result, { oneTimeOnly: true });
        // After converting the package to a URI, put it in an image element.
        document.getElementById('webviewPreview').src = url;
    };
    operation.start();  

```

### invokeScriptAsync

As an asynchronous action, executes the specified script function from the currently loaded HTML, with specific arguments.

```js
webview.invokeScriptAsync(functionName, ...args)
```
#### Parameters

**functionName**
* Type: **String**
* The name of the function to invoke. This is a property name on the global window object of the WebView's top level document. This can be a built-in global function like eval or open, or it can be a script defined function on the global window object. Only functions in the top level document of the WebView may be invoked.

**args**
* Type: **String**
* Optional string parameters to pass to the invoked function. After functionName, any additional parameters to invokeScriptAsync are strings passed to the invoked function.

#### Return value
Type: **MSWebViewAsyncOperation**

When using **invokeScriptAsync**, you need to define success and error handlers after defining the operation. After applying the event handlers, call **MSWebViewAsyncOperation.start** to execute the operation. If the MSWebViewAsyncOperation's complete event fires then the MSWebViewAsyncOperation's result property is the string return value from invoking the function. Using the invoked function's return value allows for the WebView content to communication synchronously back to the WebView host. To instead have the WebView content communicate asynchronously back to the WebView host see the MSWebViewScriptNotify event. If the function invoked throws an unhandled exception then the MSWebViewAsyncOperation's error event will fire. 

```js
const functionName = "eval";
const args = ["'Current URL: ' + document.location.href"];

const asyncOp = webview.invokeScriptAsync(functionName, ...args);

asyncOp.onerror = () => console.error("Error: " + asyncOp.error);
asyncOp.oncomplete = () => console.log("Result: " + asyncOp.result); // Logs 'Current URL: about:blank'
asyncOp.start();
```

### getDeferredPermissionRequests

Returns a list of deferred permission requests issued by content in the **webview** control.

```js
var sequence<PermissionRequest> = x-ms-webview.getDeferredPermissionRequests();
```
#### Parameters
This method has no parameters.

#### Return value
Type: [DeferredPermissionRequest](./webview/DeferredPermissionRequest.md)

The specified deferred permission request.

#### Additional features and requirements

|            |      |
|------------|------|
|**Minimum supported client** |Windows 10 [Windows Store apps only] |    
|**Minimum supported server** |None supported|   
|**Minimum supported phone**  |None supported |    


### getDeferredPermissionRequestById

Returns the specified deferred permission request. 

```js
var deferredPermissionRequest = x-ms-webview.getDeferredPermissionRequestById(id);
```
#### Parameters
*id*
* Type: **unsigned long**
* The ID of the deferred permission request you wish to get.

#### Return value
Type: [DeferredPermissionRequest](./webview/DeferredPermissionRequest.md)

The specified deferred permission request.

#### Additional features and requirements

|            |      |
|------------|------|
|**Minimum supported client** |Windows 10 [Windows Store apps only] |    
|**Minimum supported server** |None supported|   
|**Minimum supported phone**  |None supported | 

### goBack

Navigates the **webview** to the previous page in the navigation history. 

```js
webview.goBack();
```
#### Parameters
This method has no parameters.

#### Return value
This method does not return a value.

### goForward

Navigates the **webview** to the next page in the navigation history. 

```js
webview.goForward();
```
#### Parameters
This method has no parameters.

#### Return value
This method does not return a value.

### navigate

Loads the HTML content at the specified Uniform Resource Identifier (URI). 

```js
webview.navigate(uri);
```
#### Parameters

**uri**
* Type: **String**
* The URI to load.

#### Return value
This  method does not return a value.

### navigateFocus

Navigates focus onto the **webview**. Fires the webview's top level document's window's navigatingfocus event. The FocusNavigationEvent args used in the navigatingfocus event match the parameters provided to navigateFocus except the origin parameters are translated from the host document's coordinate space to the coordinate space of the webview's top level document. See the webview departingFocus event and corresponding window.departFocus method for transferring focus from the webview to the host. See the [TVJS's Direction Navigation library](https://github.com/Microsoft/TVHelpers/wiki/Using-DirectionalNavigation) for an example of an implementation of focus navigation via keyboard or gamepad that uses this method.

```js
const activeElementBounds = document.activeElement.getBoundingClientRect();
const origin = { 
    originLeft: activeElementBounds.left,
    originTop: activeElementBounds.top,
    originWidth: activeElementBounds.width,
    originHeight: activeElementBounds.height
};
webview.navigateFocus(navigationReason, origin);
```
#### Parameters
*navigationReason*
* Type: **String**
* The reason for the navigation. The value should be either "left", "up", "right", or "down". It is the direction in which focus is moving away from the currently focused element.

*origin*
* Type: **Object**
* The origin of the navigation. This is a JavaScript object with properties "originLeft", "originTop", "originWidth", and "originHeight". These values should describe the position and size of the currently focused element away from which focus is moving.

#### Return value
This method does not return a value.

### navigateToLocalStreamUri

Loads local web content at the specified URI using a [**UriToStreamResolver**](https://docs.microsoft.com/en-us/uwp/api/windows.web.iuritostreamresolver.uritostreamasync).

```js
webview.navigateToLocalStreamUri(source, streamResolver); 
```
#### Parameters

*source*
* Type: **String**
* An ms-local-stream URI identifying the local HTML content to load. Create this string using [**buildLocalStreamUri**](https://docs.microsoft.com/en-us/uwp/api/windows.web.ui.iwebviewcontrol.buildlocalstreamuri).

*streamResolver*
* Type: any
* A resolver that converts the URI into a stream to load.

#### Return value
This method does not return a value.

### navigateToString

Loads the specified HTML content as a new document. 

```js
webview.navigateToString(contents);
```
#### Parameters

*contents*
* Type: **String**
* The HTML content to display.

#### Return value
This method does not return a value.

### navigateWithHttpRequestMessage

Navigates the webview to a Uniform Resource Identifier (URI) with a POST request and HTTP headers. 

```js
webview.navigateWithHttpRequestMessage(requestMessage);
```
#### Parameters

*requestMessage*
* Type: **HttpRequestMessage**
* The details of the HTTP request. 

#### Return value
This method does not return a value.

#### Remarks

> [!WARNING]
> If you add additional headers to this request, such as authentication credentials, remember that those headers will also be included with any subsequent child requests. Use caution to prevent accidental disclosure of confidential or personal information. 


### refresh 

Reloads the current content in the **webview**. 

```js
webview.refresh();
```
#### Parameters
This method has no parameters.

#### Return value
This method does not return a value.


### stop

Halts the current **webview** navigation or download. 

```js
webview.stop();
```
#### Parameters
This method has no parameters.

#### Return value
This method does not return a value.


## Properties

### canGoBack

Gets a value that indicates whether the **webview** can navigate backward.

This property is read-only.

```js
var canGoBack = webview.canGoBack;
```

#### Property value
Type: **Boolean**

**True** if the **webview** can navigate backward; otherwise, **false**.

### canGoForward

Gets a value that indicates whether the **webview** can navigate forward.

This property is read-only.

```js
var canGoForward = webview.canGoForward;
```

#### Property value
Type: **Boolean**

**True** if the **webview** can navigate forward; otherwise, **false**.

### containsFullScreenElement

Gets a value that indicates whether the **webview** contains an element that supports full screen. See the MSWebViewContainsFullScreenElementChanged event for more info.

This property is read-only.

```js
var containsFullScreenElement = webview.containsFullScreenElement;
```

#### Property value
Type: **Boolean**

**True** if the **webview** contains an element that supports full screen; otherwise, **false**.


### documentTitle

Gets the title of the page currently displayed in the **webview**. 

This property is read-only.

```js
var documentTitle = webview.documentTitle;
```

#### Property value
Type: **String**

The page title.

### height

Gets or sets the height of the **webview**. 

```js
var height = webview.height;
webview.height = height;

```

#### Property value
Type: **Number**

The height of the **webview**. 


### process

Gets the **webview** process.

This property is read-only.

```js
var process = webview.process;
webview.process = process;
```

#### Property value
Type: [MSWebViewProcess](./webview/MSWebViewProcess.md)


### settings

Gets a [MSWebViewSettings](./webview/MSWebViewSettings.md) object that contains properties to enable or disable **webview** features.

This property is read-only.

```js
var settings = webview.settings;
webview.settings = settings;
```

#### Property value
Type: [MSWebViewSettings](./webview/MSWebViewSettings.md)

A [MSWebViewSettings](./webview/MSWebViewSettings.md) object that contains properties to enable or disable **webview** features.

### src

Gets or sets the Uniform Resource Identifier (URI) source of the HTML content to display in the **webview** control. 

```js
var src = webview.src;
webview.src = src;
```

#### Property value
Type: **String**

The URI source of the HTML content to display in the **webview** control. 


### width

Gets or sets the width of the **webview**.

```js
var width = webview.width;
webview.width = width;
```

#### Property value
Type: **Number**

The width of the **webview**.