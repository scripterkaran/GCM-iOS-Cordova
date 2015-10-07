# GCM Push Notifications for Cordova (iOS)

## Description

Implementation of Push Notifications using GCM that Google featured in their 2015 Google I/O conference: https://developers.google.com/cloud-messaging/

### Attention!

- This plugin only works with the latest Cordova 5 release
- This project is forked from https://github.com/gonzaloaune/GCMPushPlugin.git, but improved to get more usability on iOS part.

### Installation
`cordova plugin add  cordova-plugin-gcm-ios`

Assuming you have your Cordova application up and running:

1) Go to https://developers.google.com/cloud-messaging/ios/start and generate the configuration file and upload your APNS certificates.

2) Put your configuration file in the root of your project and make sure to add it to your target as advice by Google.

3) Go to https://developers.google.com/cloud-messaging/ios/client and follow the POD installation.

Following Points are just summary of how to get started with POD configurations:

-Make sure you have properly configured PODFile. It should Look like 

```
# Uncomment this line to define a global platform for your project
platform :ios, '7.0'

target 'HelloCordova' do
pod 'Google/CloudMessaging'
end
```
And Make sure to do `pod install`

And Open only `.xcworkspace` for future development from Xcode


4) Run `cordova plugin add  cordova-plugin-gcm-ios` to install the plugin

### Registration

The `register` method will register your Token with GCM. The way to call it is the following:

```js
window.GcmPushPlugin.register(successHandler, errorHandler, {
    "badge":"true",
    "sound":"true",
    "alert":"true",
    "usesGCM":true,
    "sandbox":true,
    "jsCallback":"onNotification"
});
```

The first parameter is the callback that will be fired once the Token is generated, the token will come in a json where the key is `gcm`.
```js
function successHandler(result) {
  console.log("Token: " + result.gcm);
}
```
The second parameter is the callback that will be fired if there was an error while generating the Token.
```js
function errorHandler(error) {
  console.log("Error: " + error);
}
```
The third parameter is a hash of options, in this case we have the following:
- 3 parameters that Apple asked us to register for push notifications (`badge`, `sound` and `alert`)
- `usesGCM` will tell the plugin if we want to register the device with GCM or using the native iOS method
- `sandbox` will tell the plugin to register using the sandbox or production environment.
- `jsCallback` is the callback to be called once we get our notification.

If everything goes well and you are able to register, to send a push notification you should do something like this using cURL (change **SERVER_API_KEY** and **DEVICE_TOKEN** where appropiate):

```
curl --header "Authorization: key=SERVER_API_KEY" \
       --header Content-Type:"application/json" \
       https://gcm-http.googleapis.com/gcm/send \
       -d "{ \"data\" : { \"title\" : \"MyCoolApp\", \"text\" : \"MessageText\", \"extra\":{\"url\":\"someurl.js\"}}, \"to\" : \"$DEVICE_TOKEN\" }"
```

Then you will receive in your `onNotification` method, the whole json you pass to GCM in the cURL command:

```js
function onNotification(notification) {
  console.log("Event Received: " + e); // { "extra": {"url" : "someurl.js" } } 
}
```
### Subscribe To Topics
```js
    window.GcmPushPlugin.subscribeToTopic(successHandler, errorHandler, topic)
```
The parameter 'topic' is a string. Example '/topics/global'
### Unsubscribe To Topics
```js
    window.GcmPushPlugin.unsubscribeToTopic(successHandler, errorHandler, topic)
```
The parameter 'topic' is a string. Example '/topics/global'


### Unregister

The `unregister` method will unregister your Token from GCM. The way to call it is the following:

```js
window.GcmPushPlugin.unregister(unregisterSuccess, unregisterError);
```

The first parameter is the callback that will be fired once the unregistration is successful.
```js
function unregisterSuccess(result) {
  console.log("Unregister success: " + result);
}
```
The second parameter is the callback that will be fired if there was an error while unregistering.
```js
function unregisterError(error) {
  console.log("Error: " + error);
}
```

If everything goes well and you are able to unregister, you won't be able to send a push notification anymore getting a NotRegistered error from GCM:

```
curl --header "Authorization: key=SERVER_API_KEY" \
       --header Content-Type:"application/json" \
       https://gcm-http.googleapis.com/gcm/send \
       -d "{ \"data\" : { \"title\" : \"MyCoolApp\", \"text\" : \"MessageText\", \"extra\":{\"url\":\"someurl.js\"}}, \"to\" : \"$DEVICE_TOKEN\" }"
```
`{"multicast_id":xxxxxxxxxxxxxxx,"success":0,"failure":1,"canonical_ids":0,"results":[{"error":"NotRegistered"}]}%`

### setApplicationIconBadgeNumber

The `setApplicationIconBadgeNumber` method will set (as the name says) the application badge icon number to whatever you want. The way to call it is the following:

```js
window.GcmPushPlugin.unregister({'badge':12});
```


```
The MIT License

Copyright (c) 2015 

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
