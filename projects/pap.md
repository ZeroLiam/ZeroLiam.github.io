---
layout: default
title: Phone As Pixel
permalink: /projects/pap
---

# {{ page.title }}

<div class="project-content">
  {% include projectbasic.html project=site.data.projects link="/projects/pap"%}
</div>

* [Description - Phone As Pixel Web SS17](#description)
* [Challenge](#challenge)
* [Approach, Tech & Frameworks](#approach)
* [Installation and Use](#installation)
* [Server](#server)
* [Client (Devices)](#client-devices)
* [Camera Manager](#camera-manager)
* [Recommendations, Notes, Limitations](#recommendations)
* [Conclusion](#conclusion)
* [References](#references)

---
# [Description - Phone As Pixel Web SS17](#description)

## I'll be updating the wiki as long as I'm developing this
## Link to [wiki](https://github.com/ZeroLiam/PAP/wiki)

Hello! Please feel free to comment/ask/send me a message in case of any questions. Thanks!

UPDATE: I got interested in how to improve the original concept, so after I submit this for the class I will be implementing new features. If you're interested in contributing, feel free to fork and let me know.

## Concept
What if you and a group of friends cheer for your favorite team by using a handmade banner? It can be really fun: you can use many cardboards and write the letters of your team's name, then stand next to each other on the stadium and raise them up for the team to see.
A similar concept is behind **Phone As Pixel**: smaller mobile devices are put together on an area in front of a camera and they display a part of the same big image. The connectivity is not only on the phones but also on the people participating on this.

> "I used to be a young inspired developer like you, **until I took a code to the knee**" - Liselot after finishing this project

**_This is my attempt to do so... I've cried a lot and destroyed my hands trying to make this code to work!_**

---
# [Challenge](#challenge)

Basically, this project has three main enemies:

* **Time of update** between the image manager (the one that sends the image, by URL or file upload) and the devices.
  First, the devices need to be discoverable and be registered through the image manager to the server. It depends mostly in the time they connect to the localhost: once the connection is made, the phones trigger a signal to the camera to notify they are ready to be seen and receive the picture.
  The original implementation had a system of color flickering, but in this case I assign randomly one color between magenta and cyan (please see the [Approach, Tech, & Frameworks](#approach) section for further explanation).
* **Send and receive** data between devices, server, and image manager(camera).
  The process should be done in less than five seconds, or as soon the camera can detect the phone. Once this is done the server should have the data from the phones and their acknowledgement on the camera field view, concluding by sending the image to all of them.
* **Screen glare and outside light** makes difficult to identify the devices by making encoded color transitions.
  Finally, mobile systems have screens with internal light on them, which makes it difficult to recognize the  color flickering or color assignment on them because in the camera the colors get lost on the glare. This is still the biggest challenge to solve regardless of the model of the device.

---
# [Approach, Tech & Frameworks](#approach)

### Approach
Both image manager and devices need to communicate to the server to exchange data. This is the implementation used to solve this issue:
1. As soon as a client connects the server assigns them an ID, a color, and a default image (just in case).
2. Then, once the devices are connected and decide on which color to display, they send an object back to the server. This object contains the data for the phone as a client, including `image`, `color`, `connection`, `position x`, `position y`, `width`, `height`, and `display`.
3. The server gets the object from the clients once they emit a function called `devices`. Here, they prepare a local object called `clientObj`, and once it's ready it orders the camera to recognize that device with those characteristics by triggering `getcolors`.
4. The image manager (camera and image selector) needs to recognize the devices by their color, position, width and height. To initiate this functionality, the Uploader component is available for use: you input a URL and when it's ready you can send the image to the phones.
5. Once an image is chosen, the app takes roughly four (4) seconds to recognize the position, and when it finishes it sends the object back to the clients.
6. Each object contains a modified position for the phones in respect of the camera view, so when the object is back to the device it is the client/device that decides how to scale and where to put the image.

### Note about ReactJS
Most of the time I had to code a local object or variable copying something that is sent; the case is that I cannot modify the state of the component in React because it creates conflict in their lifecycle, in consequence this breaks the app. These issues can come up in situations for the camera, since it's recording live and pushing data to `event.data`, the morphosis of the React state cannot keep up or re-render on time, causing a chaos.

TL;DR don't break it unless you know how to handle React lifecycle.

### Tech Needed
* **Multiple mobile devices (phones, tablets, etc)**. Laptops and other screens can be used too, however I have not tested with them yet, but it is possible.
* **Camera**. It can be a built in camera on a computer or another phone, but a common usb webcam is recommended because of its flexibility to move around.

### Development Frameworks
* **JavaScript/HTML/CSS**
* **ReactJS** is a powerful modular user-interface library that is robust and stable for web apps, specially on mobile. Instagram uses React Native for its crossplatform app, and the push notification benchmark was the one that made me choose the ReactJS version for speed on this project. For more on how React Native is implemented on Instagram, please visit this [case study](https://engineering.instagram.com/react-native-at-instagram-dd828a9a90c7).
* **Socket.io** is a manager that provides the server via websockets.
* **TrackingJS** was used for color and phone tracking on the live video feed.
* **Libraries** Other libraries such as _jQuery_ and _lodash_ were used to write this code faster, I'm not gonna lie.

### Diagram

![PAP Diagram](https://drive.google.com/open?id=0B5zy3ARus4EXM05yYVhSUE5oVTA)
In case the image doesn't show, here's the link to the diagram: [PAP Diagram](https://drive.google.com/open?id=0B5zy3ARus4EXM05yYVhSUE5oVTA)

---
# [Installation and use](#installation)

## Requisites
1. On your terminal, install NodeJS [via package manager](https://nodejs.org/en/download/package-manager/) or by [download](https://nodejs.org/en/download/).
2. After installing Node, install npm by following their [guide online](https://www.npmjs.com/get-npm)
3. Finally, clone the PAP repo like on the following part.

## With the repo
 1. Git clone the project on your terminal `git clone https://github.com/ZeroLiam/PAP.git`
 2. First, go to the server on `PAP/socketio-server/`. Do an NPM install to make sure you have all the resources needed to run it. Use `npm install --save-dev`.
 3. Second, go to the `PAP/phoneaspixelweb/` directory. Do the same NPM install (`npm install --save-dev`) on this folder.
 4. **_LISTEN!_** As soon as you do it, and in order to avoid errors when running the app, go to the `package.json` file and modify the line
 ```javascript
    "start": "react-scripts start"
 ```
 with
 ```javascript
    "start": "node node_modules/react-scripts/scripts/start.js start"
  ```
  5. Once this is done, go to the folder for the server (`PAP/socketio-server/`) and run
  ```javascript
     node ws_server.js
  ```
  6. In another terminal window or tab, go to the react app (`PAP/phoneaspixelweb/`) and run
  ```javascript
     npm start
  ```
  7. When it opens the browser, you'll see the whole background with a color. That's fine, that means that a client was created, however, we need to go to the image/camera-manager to preset an image to send to the phones. To do so, go to `localhost:3000/cam`.
  8. On your phone(s), type `localhost:3000` on a browser and put them in front of the camera view.
  9. You're ready to play!

---
# [Server](#server)

## Require

The server needs `express`, `http`, and `socket.io` modules for starters. It also has a `config` object with the host, port, and protocol needed to assemble the connection.

Variables for objects are needed for the camera, another from devices/clients, and a test image url just in case the url uploaded fails.

The server also generates a random color and sends it to the client once it's connected. TrackingJS also provides a way to register a new color, something that I tried to implement but that's another beast to tame, and I was using my time to make the alignment work. Feel free to extend this feature.

## On connection
* Makes an object assigning an ID, a color from `getRandomPresetColor()`, a `connected: true` notification, a message, and just in case, a predetermined image, then it sends it back to the client who connected.

It has the following listeners for the devices and the camera:

### Set Data
* Sends the generated data to the specific client id by accessing to the list of clients connected.

### Disconnect
* On disconnect it pops the client out of the list of clients for the camera, so we don't have the problem of the app breaking because a minion was gone with the wind.

### Get Colors
* Instead of a request, this actually sends all the updated devices to BigBro™ (a.k.a. the camera manager).
* Once the camera recognizes them, it sends back an object updated with `width`, `height`, `position` to this server. I called it `dummy` as in `dummy dolls` for all the devices, and it sends back the object from the camera to the device.
Seriously, I don't want to offend your device.

### On reloadClients
* A button on the camera url is available in case you want to try again with more phones, other positions, or even a new image, this function reloads all the clients including the camera.

### Simple?
I think so, but if you know a better way, again, feel free to do so.

---
# [Client (Devices)](#client-devices)

This is what is shown in `localhost:3000`. The devices should connect directly to this.

## Constructor
The constructor here is simple: three general variables to keep track of the scale of the image (should be outside of the state, because, again, **DO NOT** mess with React's lifecycle), and a state with an object for the data to be exchanged.

## componentDidMount - Where the magic happens

### On Connect
Here a local object gets the server preset once it's connected. The part `socketio.id` generates an ID of client, then send its to the server. It changes every time a new connection happens, even in refresh of the page. Don't worry, the ID is unique and the server pops out the clients that leave and keeps a track of them. The new ID is matched with its properties when the refresh happens and connects again to the server.

### Set Data
This is the one that prepares the object with all its properties and sends it to the camera over the server. Once the camera tracks the properties, the server is the one that matches those properties with the corresponding ID from the client, which is made by this part on the client.

### Set Position
After the camera identifies the device and the server resolves who is who, then this is the one that gets the updated object.

On `newDataState` is when the state of the client actually changes. Here, all the data received from the camera manager over the server updates the state characteristics, and `this.setState` transfer that data to the state object.

### Should Reload?
This triggers the window reload once the button for reload is clicked on the camera manager.

### Disconnect
In here, the state is cleared and it's ready for a new set of data. This is **very** important because the socket id gets popped out from the list on the server, so to make sure it doesn't have a conflict on a new connection it is healthy to clear the object on disconnect.

## Render, Scale, Position
Based on the data received, the client makes the render, scale and position for the image.

### Render Function
When the window is loaded, the object `maindiv` is created with the size of the viewport of the browser. This is important to display in full screen the image and the color on connection.

The new size, scale, and position are calculated here, then passed to the object `imgdiv`. This object creates the CSS with the new properties to display the image accordingly to size and position on the final render.

The clients have at the end only two `div`s, all the magic happens in the React lifecycle and the server.

Want to contribute? Collaborate and let me know if you can improve this project!

---
# [Camera Manager](#camera-manager)

## Setup
On this component, the basic structure is:
1. A constructor that has global variables for video, camera, context, video's width and height, a local object (from the device object from the server), and an image source.
2. In the same constructor, a state with the data (which will be the permanent object from the device data), another image source, and an approval for sendImage. Remember on [Approach, Tech & Frameworks](#approach) when I said that modifying the state can mess with React's lifecycle and the app? Well, after struggling with the image and trying to get to load for a canvas to make the alignment work, I realized that you **CANNOT** modify a state during a load callback.

> "TL;DR, *DO NOT* mess with React's lifecycle" - Young Liselot after not sleeping for a long time

## On Update Image
This is triggered by the upload/url component and starts the camera once we have the image loaded.

## Receiving/Sending Data to Server
Remember that you shouldn't mess with the lifecycle? Well, PROTIP: If you're making an ajax call or anything related to the server, this is done in the `componentDidMount` part of the lifecycle.

> "Comply with React's Lifecycle and your life will be better too" - Inner voice in my head
### More on React's Lifecycle

[React's Lifecycle on TutorialsP Point](https://www.tutorialspoint.com/reactjs/reactjs_component_life_cycle.htm). To me this is the best guide.

## Camera Tracker

Listen, this looks long but it isn't.

The function camtrack makes a `dummy` object (dummy as in dummy dolls, we talked about this in the server, don't get offended). This object contains the `position` and `size` for the colors to be tracked by the camera.

Here we finally use the TrackingJS component. A variable called `colors` is a ColorTracker that contains the magenta and cyan used by the app, and it's ready to capture everything on those tones on the camera view.

The `track` event creates the canvas and context to see where the phones are in the camera's view. For each event, it creates a box with the corresponding color and stores the size and position found on the camera.

After that, a 4.5 second timer is set to get the final positions from the devices. **Don't move the phones after this!** Once it's done, it sents the newly found data to the server, and the server sends it to the devices in order to display the image accordingly.

For more on the server, please go to the [server section](#server).

## Create Custom Color for Color and Object Tracking
You can create your own color and pass it to the Camera Tracker if you want. Since I was using more time in the alignment bug, I could not implement it but the function is written. Feel free to use it.

## Uploader component
This baby uses a url input and a send button to distribute the image chosen by the user to the devices. It can implement a feature for uploading files, it has the feature

## Reload Clients
Hey, if you wanna try again, just press this button to reload everyone connected.

---
# [Recommendations, Notes, Limitations](#recommendations)

1. Learn the lifecycle of ReactJS and this project will be as smooth as a brand new balloon.
2. Lifehack: put some dark plastic on the eye of the camera to avoid the glare and light.
3. Take a deep breath and a break; sometimes it's frustrating to deal with JS frameworks, and the brain gets clouded by it.
4. My "White Whale" is to find a way to get the imaginary origin x/origin y position for the image once it's sent to the phones; this can be the perfect way to position the image on all the phones. I know a bit of math but if you know more math than Calc 1, I bet you can find and kill this whale soon.

> "Comply with React's Lifecycle and your life will be better too" - Inner voice in my head
## More on React's Lifecycle

[React's Lifecycle on TutorialsP Point](https://www.tutorialspoint.com/reactjs/reactjs_component_life_cycle.htm). To me this is the best guide.

---
# [Conclusion](#conclusion)

I loved this project and it's my puppy after all these months. I coded this project in its entirety with the goal of making it scalable and simple, but I will keep maintaining it once I find new ideas to bring to it.

For future developers, I hope you guys can find a way to improve the crop, scale and position in this project. Please drop a line and let me know how it goes.

---
# [References](#references)

1. Schwarz, Julia, et al. “Phone as a Pixel.” _Proceedings of the 2012 ACM Annual Conference on Human Factors in Computing Systems - CHI '12, 2012, doi:10.1145/2207676.2208378_. [Phone As Pixel](http://dl.acm.org/citation.cfm?id=2208378)
