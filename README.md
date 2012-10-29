# FriendMapper

FriendMapper is a WebGL application that graphs your friendship connections across a virtual 3D globe.

It uses the Facebook Graph API to poll the hometowns, workplaces, and universities of your friends. It then resolves these locations into GPS coordinates and plots "arcs" that represent a connection across the globe.

You can click on an individual arc to obtain more information about the location and connection it represents.

<img src="http://petercottle.com/fm1.png"/>

<img src="http://petercottle.com/fm2.png"/>

## Challenges

* FriendMapper uses barebones WebGL -- no Three.js or any other framework is leveraged. I initially looked into Three.js but soon realized that the performance of the framework wasn't sufficient (at the time). Using bare WebGL means that I have to generate the vertices for the arcs and load them onto the GPU manually, as well as write shader programs and individually animate each arc "streaming" across the screen.

* Since there are numerous Graph API calls during application startup, all the data arrives asychronously and out of order. For example, the GPS coordinates of "UC Berkeley" might arrive before finding out one of your friends studied there -- additionally, if the Facebook Graph API can't resolve the GPS coordinates of a location, Yahoo's YQL is polled as a last-ditch attempt. The end result of this async madness was to create a framework that could handle the arrival of data in any order, and when ready, generate the arc vertices and load those onto the GPU.

* Finally, clicking on an arc involves a ton of behind-the-scenes trickery. There are no event handlers for WebGL elements because all the browser sees is a bunch of pixel data on a canvas. Consequently, in order to determine which arc was clicked underneath a mouse click event, the entire scene is rendered into an off-screen buffer with a different shader program. This different shader program renders each arc with a unique color (RGB string) and uses flat shading. After the scene is rendered, the color underneath the mouse click event is calculated and used to lookup the associated arc. After this process is done, the necessary arc is animated and DOM elements are overlaid to show information to the user.

## Video

If you would like to see a demo of the application rather than logging in (and launching a WebGL app), a short youtube video is available [here](http://www.youtube.com/watch?v=z4nVX-ySwH4)

## Disclaimer -- Query Rate Limit

This application was written before Facebook introduced the 'additional fields' feature for the Graph API. This means that I have to perform two roundtrips to lookup all the hometowns and universities for a user's friends, and then a third roundtrip to get GPS coordinates. Even though these queries are batched, I often exceed the maximum request rate for the Graph API and the application only displays a portion of the available data.




