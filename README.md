# Qt-Server-Sent-Events-Demo

A Qt5 Server Sent Events (SSE) demo.

## Build instructions

- `cmake CMakeLists.txt`
- `make`
- `./qt-sse-demo <URL>`

## Demo

**Setup SSE server**

This repo includes a simple SSE server demo in the `server` directory.
To run it, you need NodeJS and Yarn installed:

- `cd server`
- `yarn install`
- `yarn start`

The demo server runs on port `3000` and publishes events on `/events`.

**Demo time!**

Start the Qt SSE demo with our SSE demo server as URL:

`./qt-sse-demo http://localhost:3000/events`

```
[dylan@desktop Qt-Server-Sent-Events-Demo]$ ./qt-sse-demo http://localhost:3000/events
Qt Server Sent Events demo, written by Dylan Van Assche.
Creating new Network::Manager
GET resource: QUrl("http://localhost:3000/events")
Received event from stream
"{\"timestamp\":\"Wed Jun 17 2020 19:09:17 GMT+0200 (Central European Summer Time)\",\"message\":\"Hello World!\"}"
-----------------------------------------------------
Received event from stream
"{\"timestamp\":\"Wed Jun 17 2020 19:09:21 GMT+0200 (Central European Summer Time)\",\"message\":\"Hello World!\"}"
-----------------------------------------------------
Received event from stream
"{\"timestamp\":\"Wed Jun 17 2020 19:09:26 GMT+0200 (Central European Summer Time)\",\"message\":\"Hello World!\"}"
-----------------------------------------------------
```

## Adding to a QT Quick application
Copy the `networkmanager.cpp` and `networkmanager.h` files to your QT Quick application's root directory then add them to the project (Sources > Add Existing Files)

In main.cpp, include the QMLContext library and this library's header at the top:
```cpp
#include <QQmlContext>
#include "networkmanager.h"
```

In `main()`, after the engine has been defined (`QQmlApplicationEngine engine;`) but _before_ the engine loads the file (`engine.load(url);`), set up the SSE manager and add it to the application context:
```cpp
Network::Manager *sse_manager = Network::Manager::getInstance();
engine.rootContext()->setContextProperty("sseManager", sse_manager);
```

To open the connection in your QML file:
```js
function eventHandling(event){
    // do something with the event data
    console.info(event)
    // events are raw strings
    // if the event data includes an event and message then the event will be formatted as:
    //    "event_name: event_message
}

Component.onCompleted: {
    // connect the function to the signal
    sseManager.onEventData.connect(eventHandling)
    // open the connection to the host
    sseManager.getResource("http://eventserver/eventpath")
}
```